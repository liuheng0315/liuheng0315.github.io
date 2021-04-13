---
title: JVM笔记-07JVM内存查看命令
date: 2020-12-07 08:54:32
tags:
	- jvm
categories: 
	- jvm
cover: /images/jvm.jpg
typora-root-url: ../..
---

## JVM内存查看命令

### 内存分析命令

- jstat -gc PID，jstat -gc PID 180000 10 这就相当于是让他每隔三分钟执行一次统计，连续 执行10次
  - S1C：这是To Survivor区的大小 
  - S0U：这是From Survivor区当前使用的内存大小 
  - S1U：这是To Survivor区当前使用的内存大小 
  - EC：这是Eden区的大小 
  - EU：这是Eden区当前使用的内存大小 
  - OC：这是老年代的大小 
  - OU：这是老年代当前使用的内存大小 
  - MC：这是方法区（永久代、元数据区）的大小 
  - MU：这是方法区（永久代、元数据区）的当前使用的内存大小 
  - YGC：这是系统运行迄今为止的Young GC次数 
  - YGCT：这是Young GC的耗时 
  - FGC：这是系统运行迄今为止的Full GC次数 
  - FGCT：这是Full GC的耗时 GCT：这是所有GC的总耗时

### jmap和jhat摸清线上系统的对象分布

- jmap -heap PID  这个信息会打印出来堆内存相关的一些参数设置，然后就是当前堆内存里的一些基本各个区域的情况
- jmap -histo PID  他会按照各种对象占用内存空间的大小降序排列，把占用内存最多的对象放在最上面
- jmap -dump:live,format=b,file=dump.hprof PID 这个命令会在当前目录下生成一个dump.hrpof文件
- jhat dump.hprof -port 7000 用jhat去分析堆快照，会支持通过浏览器来以图形化的方式分析堆转储快照