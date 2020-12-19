---
title: mysql笔记02-binlog日志
date: 2020-12-17 20:31:14
categories: 
		- 数据库
tags: 
	- mysql
cover: https://ss0.bdstatic.com/70cFvHSh_Q1YnxGkpoWK1HF6hhy/it/u=138264622,1709438672&fm=26&gp=0.jpg
typora-root-url: ../..
---

## binlog日志

### MySQL binlog到底是什么？

​		之前说的redo log，他是一种偏向物理性质的重做日志，因为他里面记录的是类似这样的东西，“对哪个数据页中的什么记录，做了个什么修改”。而且redo log本身是属于InnoDB存储引擎特有的一个东西。而binlog叫做归档日志，他里面记录的是偏向于逻辑性的日志，类似于“对users表中的id=10的一行数据做了更新操 作，更新以后的值是什么”，binlog不是InnoDB存储引擎特有的日志文件，是属于mysql server自己的日志文件。

### 提交事务的时候，同时会写入binlog

​		提交事务的时候，会把redo log日志写入磁盘文件中去。然后其实在提交事务的时 候，我们同时还会把这次更新对应的binlog日志写入到磁盘文件中，

### binlog日志的刷盘策略

​		binlog日志，其实也有不同的刷盘策略，有一个sync_binlog参数可以控制binlog的刷盘策略，他的默认值是0， 此时你把binlog写入磁盘的时候，其实不是直接进入磁盘文件，而是进入os cache内存缓存。如果此时机器宕机，那么你在os cache里的binlog日志是会丢失的。把sync_binlog参数设置为1的话，那么此时会强制在提交事务的时候，把binlog直接写入到磁盘文件里去， 那么这样提交事务之后，哪怕机器宕机，磁盘上的binlog是不会丢失。

### 基于binlog和redo log完成事务的提交

​		当我们把binlog写入磁盘文件之后，接着就会完成最终的事务提交，此时会把本次更新对应的binlog文件名称和这次 更新的binlog日志在文件里的位置，都写入到redo log日志文件里去，同时在redo log日志文件里写入一个commit标 记。 在完成这个事情之后，才算最终完成了事务的提交。

### 最后一步在redo日志中写入commit标记的意义是什么？

​		必须是在redo log中写入最终的事务commit标记了，然后此时事务提交成功，而且redo log里有本次更新对应的日 志，binlog里也有本次更新对应的日志 ，redo log和binlog完全是一致的。

### 后台IO线程随机将内存更新后的脏数据刷回磁盘

​		事务提交后磁盘上的数据文件可能还是旧值，MySQL有一个后台的IO线程，会在之后某个时间里，随机的把内存buffer pool中的修改后的脏数据给刷回到磁 盘上的数据文件里去，后台IO线程把buffer pool里的修改后的脏数据刷回磁盘的之后，磁盘上的数据才会跟内存里一样，在你IO线程把脏数据刷回磁盘之前，哪怕mysql宕机崩溃也没关系，因为重启之后，会根据redo日志恢复之前提交事务做过的修改到内存里去。