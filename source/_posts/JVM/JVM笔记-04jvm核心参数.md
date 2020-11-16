---
title: JVM笔记-04jvm核心参数
date: 2020-11-02 20:38:34
tags:
	- jvm
categories: 
	- JVM
cover: https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=1520500758,409058955&fm=26&gp=0.jpg
typora-root-url: ../..
---

## 核心参数

-Xms ：java堆内存的大小

-Xmx ：java最大可分配的堆内存

-Xmn ： 新生代的堆内存大小，扣除之后就为老年代的堆内存大小

-XX:Permsize || -XX:MetaspaceSize ：永久代大小

-XX:MaxPermsize  || -XX:MaxMetaspaceSize ：永久代最大大小

-Xss ：每个线程的栈内存大小