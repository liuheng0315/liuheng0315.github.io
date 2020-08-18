---
layout: rocketmq笔记
title: RocketMQ笔记 02-基本原理
date: 2020-08-18 17:14:46
categories: Java
tags:
	- RocketMQ
	- 中间件
cover: https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=2582113194,968310573&fm=26&gp=0.jpg
typora-root-url: ..
---

## RocketMQ基本原理

问题：

1. RocketMQ是如何集群化部署来承载高并发访问？

   将系统的流量分散在RocketMQ部署的多台机器上

2. 如果RocketMQ要存储海量消息，如何实现分布式存储架构？

   将数据打散存到每个Broker上，万一Broker宕机怎么办，采用Broker主从架构及多副本策略

RocketMQ基本架构原理图：

<img src="/images/RocketMQ%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86.png" alt="RocketMQ基本架构图" style="zoom:50%;" />



发送消息的时候面对N多台机器，到底应该向哪一台上面的Broker发送过去？消费者如何知道去哪个Broker上消费？

