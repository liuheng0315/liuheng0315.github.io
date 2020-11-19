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