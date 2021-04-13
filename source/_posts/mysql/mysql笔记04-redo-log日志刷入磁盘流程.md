---
title: mysql笔记04-redo log日志刷入磁盘流程
date: 2021-01-05 09:12:25
categories: 
		- mysql
tags: 
	- mysql
cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=138264622,1709438672&fm=26&gp=0.jpg
typora-root-url: ../..
---

## redo log日志刷入磁盘流程

redo log有自己的储存结构即为redo log block，redo log buffer是mysql启动的时候，跟操作系统申请的一块连续的内存空间，然后再里面划分出了N多个空的redo log block，redo log buffer的默认值就是16M，每一个redo log block为512kb。

平时在执行事务的一个过程中，每个事务都可能存在多个增删改操作，那么就会有多个redo log，这么多redo log就是一组redo log，其实每组redo log都现在别的地方暂存，然后都执行完了，再把一组redo log给写入到redo log buffer的block里去的，如果一组redo log实在太多了，那么就会存放在多个redo log block中

### redo log buffer中的缓冲日志什么时候写入磁盘？

1. 如果写入redo log buffer的日志已经占据了redo log buffer总容量的一半了，也就是超过了8M的redo log已经在缓冲里面了，此时就会把他们刷入到磁盘文件里去
2. 一个事物提交的时候，必须把他的那些redo log所在的redo log block都刷入磁盘文件里去，只有这样，当事务提交之后，他修改的数据绝对不会丢失，因为redo log中有重做日志，随时可以恢复事务做的修改（redo log哪怕是事务提交的时候写入磁盘文件，也是先进入os cache，进入到os的文件缓冲区里，所以是否需要提交事务就强行把redo log刷入物理磁盘文件中，这个需要设置对应的参数）
3. 后台线程定时刷新，有一个后台线程每个1秒就会把redo log buffer里的redo log block刷到磁盘文件里去
4. mysql关闭的时候，redo log block都会刷入到磁盘里去