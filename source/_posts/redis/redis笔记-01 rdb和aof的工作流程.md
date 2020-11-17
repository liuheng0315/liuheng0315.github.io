---
title: redis笔记-01 rdb和aof的工作流程
date: 2020-11-03 23:03:17
categories: 
	- redis
tags:
	- redis
cover: /images/redis.jpg
typora-root-url: ../..
---

## redis笔记

RDB：每隔几分钟生成redis内存中一份完整的快照

AOF：存放每条写命令，当大到一定的程度，制定rewrite操作，基于当时redis的内存结构，来重新构造一个更小的AOF文件，然后将旧的很大的文件删除



### RDB持久化机制的工作流程

1. redis根据配置尝试去生成rdb快照文件
2. fork一个子线程出来
3. 子进程尝试将数据dump到临时的rdb快照文件中
4. 完成rdb快照文件的生成之后，就会替换之前的旧的快照文件



### AOF持久化机制的工作流程

1. redis fork一个子进程
2. 子进程基于当前内存中的数据，构建日志，开始往一个新的临时的AOF文件中写入日志
3. redis主进程，接收到client新的写操作之后，在内存中写入日志，同时新的日志也继续写入旧的AOF文件
4. 子进程写完新的日志文件之后，redis主进程将内存中的新日志再次追加到新的AOF文件中
5. 用新的日志文件替换掉旧的日志文件

