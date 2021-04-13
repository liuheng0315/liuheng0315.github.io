---
title: RocketMQ笔记 03-OS内核参数及JVM参数调优
date: 2020-08-21 11:02:56
categories: MQ
tags:
	- RocketMQ
cover: /images/mq.jpg
---

## OS内核参数及JVM参数调优

### OS内核参数

1. vm.overcommit_memory 

   - 这个参数有0,1,2三个值可选，如果值是0的话，在你的中间件系统申请内存的时候，os内核会检查可用内存是否足够，如果足够的话就分配内存给你，如果感觉剩余内存不是太够了，干脆就拒绝你的申请，导致你申请内存失败，进而导致中间件系统异常出错。

     因此一般需要将这个参数的值调整为1，意思是把所有可用的物理内存都允许分配给你，只要有内存就给你来用，这样可以避免申请内存失败的问题，可以用如下命令修改：echo 'vm.overcommit_memory=1' >> /etc/sysctl.conf

2. vm.max_map_count

   - 这个参数的值会影响中间件系统可以开启的线程的数量，同样也是非常重要的，如果这个参数过小，有的时候可能会导致有些中间件无法开启足够的线程，进而导致报错，甚至中间件系统挂掉。他的默认值是65536，但是这个值有时候是不够的，比如我们大数据团队的生产环境部署的Kafka集群曾经有一次就报出过这个异常，说无法开启足够多的线程，直接导致Kafka宕机了。

     因此建议可以把这个参数调大10倍，比如655360这样的值，保证中间件可以开启足够多的线程，可以用如下命令修改：echo 'vm.max_map_count=655360' >> /etc/sysctl.conf

3. vm.swappiness

   - 这个参数是用来控制进程的swap行为的，这个简单来说就是os会把一部分磁盘空间作为swap区域，然后如果有的进程现在可能不是太活跃，就会被操作系统把进程调整为睡眠状态，把进程中的数据放入磁盘上的swap区域，然后让这个进程把原来占用的内存空间腾出来，交给其他活跃运行的进程来使用。

     如果这个参数的值设置为0，意思就是尽量别把任何一个进程放到磁盘swap区域去，尽量大家都用物理内存。如果这个参数的值是100，那么意思就是尽量把一些进程给放到磁盘swap区域去，内存腾出来给活跃的进程使用。默认这个参数的值是60，有点偏高了，可能会导致我们的中间件运行不活跃的时候被迫腾出内存空间然后放磁盘swap区域去。

     因此通常在生产环境建议把这个参数调整小一些，比如设置为10，尽量用物理内存，别放磁盘swap区域去。可以用如下命令修改：echo 'vm.swappiness=10' >> /etc/sysctl.conf

4. ulimit

   - 这个是用来控制linux上的最大文件链接数的，默认值可能是1024，一般肯定是不够的，因为你在大量频繁的读写磁盘文件的时候，或者是进行网络通信的时候，都会跟这个参数有关系

     对于一个中间件系统而言肯定是不能使用默认值的，如果你采用默认值，很可能在线上会出现如下错误：error: too many open files。

     因此通常建议用如下命令修改这个值：echo 'ulimit -n 1000000' >> /etc/profile

**总结**：

综合思考一下这几个参数，会发现到最后要调整的东西，无非都是跟磁盘文件IO、网络通信、内存管理、线程数量有关系的，因为我们的中间件系统在运行的时候无非就是跟这些打交道。

1. 中间件系统肯定要开启大量的线程（跟vm.max_map_count有关）
2. 而且要进行大量的网络通信和磁盘IO（跟ulimit有关）
3. 然后大量的使用内存（跟vm.swappiness和vm.overcommit_memory有关）



### JVM参数

启动Broker的参数如下:

```
“-server -Xms8g -Xmx8g -Xmn4g -XX:+UseG1GC -XX:G1HeapRegionSize=16m -XX:G1ReservePercent=25 -XX:InitiatingHeapOccupancyPercent=30 -XX:SoftRefLRUPolicyMSPerMB=0 -verbose:gc -Xloggc:/dev/shm/mq_gc_%p.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCApplicationStoppedTime -XX:+PrintAdaptiveSizePolicy -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=30m -XX:-OmitStackTraceInFastThrow -XX:+AlwaysPreTouch -XX:MaxDirectMemorySize=15g -XX:-UseLargePages -XX:-UseBiasedLocking”
```

-**server**：这个参数就是说用服务器模式启动

**-Xms8g -Xmx8g -Xmn4g**：这个就是很关键的一块参数了，也是重点需要调整的，就是默认的堆大小是8g内存，新生代是4g内存，如果高配物理机是48g内存的，这里完全可以给他们翻几倍，比如给堆内存20g，其中新生代给10g，甚至可以更多一些，当然要留一些内存给操作系统来用

**-XX:+UseG1GC -XX:G1HeapRegionSize=16m**：这几个参数至关重要，这是选用了G1垃圾回收器来做分代回收，对新生代和老年代都是用G1来回收，这里把G1的region大小设置为了16m，这个因为机器内存比较多，所以region大小可以调大一些给到16m，不然用2m的region，会导致region数量过多的

**-XX:G1ReservePercent=25**：这个参数是说，在G1管理的老年代里预留25%的空闲内存，保证新生代对象晋升到老年代的时候有足够空间，避免老年代内存都满了，新生代有对象要进入老年代没有充足内存了，默认值是10%，略微偏少

**-XX:InitiatingHeapOccupancyPercent=30**：这个参数是说，当堆内存的使用率达到30%之后就会自动启动G1的并发垃圾回收，开始尝试回收一些垃圾对象，默认值是45%，这里调低了一些，也就是提高了GC的频率，但是避免了垃圾对象过多，一次垃圾回收耗时过长的问题

**-XX:SoftRefLRUPolicyMSPerMB=0**：这个参数默认设置为0了，其实建议这个参数不要设置为0，避免频繁回收一些软引用的Class对象，这里可以调整为比如1000

**-verbose:gc -Xloggc:/dev/shm/mq_gc_%p.log -XX:+PrintGCDetails -XX:+PrintGCDateStamps -XX:+PrintGCApplicationStoppedTime -XX:+PrintAdaptiveSizePolicy -XX:+UseGCLogFileRotation -XX:NumberOfGCLogFiles=5 -XX:GCLogFileSize=30m**：这一堆参数都是控制GC日志打印输出的，确定了gc日志文件的地址，要打印哪些详细信息，然后控制每个gc日志文件的大小是30m，最多保留5个gc日志文件

**-XX:-OmitStackTraceInFastThrow**：这个参数是说，有时候JVM会抛弃一些异常堆栈信息，因此这个参数设置之后，就是禁用这个特性，要把完整的异常堆栈信息打印出来，注意选项中的减号,加号则表示启用

**-XX:+AlwaysPreTouch**：这个参数的意思是我们刚开始指定JVM用多少内存，不会真正分配给他，会在实际需要使用的时候再分配给他，所以使用这个参数之后，就是强制让JVM启动的时候直接分配我们指定的内存，不要等到使用内存的时候再分配

**-XX:MaxDirectMemorySize=15g**：这是说RocketMQ里大量用了NIO中的direct buffer，这里限定了direct buffer最多申请多少，如果你机器内存比较大，可以适当调大这个值

**-XX:-UseLargePages -XX:-UseBiasedLocking**：这两个参数的意思是禁用大内存页和偏向锁



### RocketMQ核心参数

dledger的示例配置文件：rocketmq/distribution/target/apache-rocketmq/conf/dledger，在这里主要是有一个较为核心的参数：sendMessageThreadPoolNums=16，这个参数的意思就是RocketMQ内部用来发送消息的线程池的线程数量，默认是16，其实这个参数可以根据你的机器的CPU核数进行适当增加，比如机器CPU是24核的，可以增加这个线程数量到24或者30，都是可以的。



### 全文总结

1. 中间件系统在压测或者上生产之前，需要对三大块参数进行调整：OS内核参数、JVM参数以及中间件核心参数
2. OS内核参数主要调整的地方都是跟磁盘IO、网络通信、内存管理以及线程管理有关的，需要适当调节大小
3. JVM参数需要我们去中间件系统的启动脚本中寻找他的默认JVM参数，然后根据机器的情况，对JVM的堆内存大小，新生代大小，Direct Buffer大小，等等，做出一些调整，发挥机器的资源
4. 中间件核心参数主要也是关注其中跟网络通信、磁盘IO、线程数量、内存 管理相关的，根据机器资源，适当可以增加网络通信线程，控制同步刷磁盘或者异步刷磁盘，线程数量有多少，内存中一些队列的大小