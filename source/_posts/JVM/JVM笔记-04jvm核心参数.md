---
title: JVM笔记-04jvm核心参数
date: 2020-11-02 20:38:34
tags:
	- jvm
categories: 
	- JVM
cover: /images/jvm.jpg
typora-root-url: ../..
---

## 核心参数

-Xms ：java堆内存的大小

-Xmx ：java最大可分配的堆内存

-Xmn ： 新生代的堆内存大小，扣除之后就为老年代的堆内存大小

-XX:Permsize || -XX:MetaspaceSize ：永久代大小

-XX:MaxPermsize  || -XX:MaxMetaspaceSize ：永久代最大大小

-Xss ：每个线程的栈内存大小

-XX:- HandlePromotionFailure:设置为true时，Minor GC会判断平均每个GC剩余的对象大小是否能放入永久代

-XX:PretenureSizeThreshold：大对象直接进入老年代的阈值

-XX:MaxTenuringThreshold：新生代进入老年代的年龄阈值

-XX:+UseParNewGC：新生代使用parNew垃圾回收器

-XX:ParallelGCThreads：并行垃圾回收线程的数量，一般不建议修改这个参数

-XX:G1HeapRegionSize：指定G1 region的大小

-XX:G1NewSizePercent：指定G1新生代初始占比

-XX:G1MaxNewSizePercen：指定G1新生代最大占比

-XX:MaxGCPauseMills：G1执行GC时最多让系统停顿的时间

-XX:InitiatingHeapOccupancyPercent：默认值是45%，G1老年代占据了堆内存的45%的Region的时候，此时就会尝试触发一个新生代+老年代一起回收的混合回收阶段

-XX:G1MixedGCCountTarget：就是在一次混合回收的过程中，最后一个阶段执行几次混合回收，默认值是8次

-XX:G1HeapWastePercent：G1混合回收的region达到这个设置的比例就停止混合回收

-XX:G1MixedGCLiveThresholdPercent：默认值85%，G1回收region的时候，必须存活对象小于这个设定的值才能被回收