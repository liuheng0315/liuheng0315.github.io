---
title: redis笔记-06 redis cluster集群hash slot算法
date: 2020-11-17 23:30:13
categories: 
	- redis
tags:
	- redis
cover: /images/redis.jpg
typora-root-url: ../..
---

## redis cluster集群hash slot算法

redis cluster有固定的16384个hash slot，对每个key计算CRC16值，然后对16384取模，可以获取key对应的hash slot

redis cluster中每个master都会持有部分slot，比如有3个master，那么可能每个master持有5000多个hash slot

hash slot让node的增加和移除很简单，增加一个master，就将其他master的hash slot移动部分过去，减少一个master，就将它的hash slot移动到其他master上去

移动hash slot的成本是非常低的

客户端的api，可以对指定的数据，让他们走同一个hash slot，通过hash tag来实现

![hash slot](/images/%E4%B8%80%E8%87%B4%E6%80%A7hash%E7%AE%97%E6%B3%95%E7%9A%84%E8%AE%B2%E8%A7%A3%E5%92%8C%E4%BC%98%E7%82%B9.png)

![虚拟结点实现负载均衡](/images/%E4%B8%80%E8%87%B4%E6%80%A7hash%E7%AE%97%E6%B3%95%E7%9A%84%E8%99%9A%E6%8B%9F%E8%8A%82%E7%82%B9%E5%AE%9E%E7%8E%B0%E8%B4%9F%E8%BD%BD%E5%9D%87%E8%A1%A1.png)

