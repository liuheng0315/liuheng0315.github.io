---
title: RocketMQ笔记 02-基本原理
date: 2020-08-18 17:14:46
categories: MQ
tags:
	- RocketMQ
cover: /images/mq.jpg
typora-root-url: ../..
---

## RocketMQ基本原理

问题：

1. RocketMQ是如何集群化部署来承载高并发访问？

   将系统的流量分散在RocketMQ部署的多台机器上

2. 如果RocketMQ要存储海量消息，如何实现分布式存储架构？

   将数据打散存到每个Broker上，万一Broker宕机怎么办，采用Broker主从架构及多副本策略

RocketMQ基本架构原理图：

<img src="/images/RocketMQ%E5%9F%BA%E6%9C%AC%E5%8E%9F%E7%90%86.png" alt="RocketMQ基本架构图" style="zoom:50%;" />

​	RockerMQ中的生成者和消费者自己主动去NameServer上拉取Broker信息，Broker与NameServer之间通过心跳机制保持联系，Broker每30秒向NameServer发送一次心跳，NameServer每10秒运行一个任务去检查各个Broker的最近一次心跳时间，如果某个Broker120秒都未发送过心跳，则认为该Broker已经挂掉了。

问题:发送消息的时候面对N多台机器，到底应该向哪一台上面的Broker发送过去？消费者如何知道去哪个Broker上消费？
	我们可以在创建Topic的时候指定让他里面的数据分散存储在多台Broker机器上，比如一个Topic里有1000万条数据，此时有2台Broker，那么就可以让每台Broker上都放500万条数据。然后生产者、消费者与NameServer建立TCP长连接获取路由信息，就知道哪些Topic分布在哪些Broker上面

## Broker架构原理

<img src="/images/Broker%E6%9E%B6%E6%9E%84%E5%8F%8A%E9%80%89%E4%B8%BE%E5%8E%9F%E7%90%86.png" alt="Broker架构与选举机制" style="zoom: 67%;" />

​	消费者获取数据时，Master Broker在返回消息给消费者系统的时候，会根据当时Master Broker的负载情况和Slave Broker的同步情况，向消费者系统建议下一次拉取消息的时候是从Master Broker拉取还是从Slave Broker拉取，既可能从master上拉取数据也可能从slaver上拉取数据。或者举另外一个例子，本身这个时候Master Broker上都已经写入了100万条数据了，结果Slave Broke不知道啥原因，同步的特别慢，才同步了96万条数据，落后了整整4万条消息的同步，这个时候你作为消费者系统可能都获取到96万条数据了，那么下次还是只能从Master Broker去拉取消息。

Slaver Broker假如宕机了对整个MQ的影响不大，因为Master Broker可以支持读写，但是Master Broker挂了，会导致该节点不可用，需要运维人员手动切换Slaver Broker为Master Broker，会导致中间一段时间不可用，在RocketMQ 4.5后，此时一旦Master Broker宕机了，就可以在多个副本，也就是多个Slave中，通过Dledger技术和Raft协议算法进行leader选举，直接将一个Slave Broker选举为新的Master Broker，然后这个新的Master Broker就可以对外提供服务了。



## Broker主从数据一致

​	关于rocketmq的数据同步分为2种模式，一种为异步复制，一种为同步复制，当为异步复制模式时，只要成功写入master，则会相应生成者success，同时会与slave慢慢同步数据，这种的优点的是低延迟，高吞吐，但是此模式可用性降低（如果此时master宕机，而数据没有完全同步到slave）。如果异步复制，此时要求成功写master后，同时还要求slave也成功写入，然后slave应答master ok后，master才会应答生产者success，这种模式可用性增加，但是延迟增加，吞吐率下降。具体选择哪种模式 还是需要根据应用场景决定



## Broker与NameServer通信

​	在RocketMQ中，Broker会跟每个NameServer建立TCP长连接，然后定时通过TCP长连接发送心跳过去，这样每个NameServer都知道集群中有哪些Broker，每个Broker存放了哪些Topic数据



## 生产者、消费者如何发送、拉取消息

<img src="/images/RocketMQ%E7%94%9F%E4%BA%A7%E8%80%85%E6%B6%88%E8%B4%B9%E8%80%85%E6%8B%89%E5%8F%96%E6%B6%88%E6%81%AF.png" alt="生产者消费者发送拉取消息" style="zoom:80%;" />

- 生产者：在发送消息时首先得有一个Topic，发送消息时得指定发送到哪个Topic里面，首先与NameServer建立TCP长连接，然后拉取到最新的路由信息，包括集群里有哪些Broker，集群里有哪些Topic，每个Topic都存储在哪些Broker上，然后生产者系统自然就可以通过路由信息找到自己要投递消息的Topic分布在哪几台Broker上，此时可以根据负载均衡算法，从里面选择一台Broke机器出来，比如round robine轮询算法，或者是hash算法。最后选择一台Broker之后，就可以跟那个Broker也建立一个TCP长连接，然后通过长连接向Broker发送消息即可
- 消费者：其实跟生产者系统原理是类似的，他们也会跟NameServer建立长连接，然后拉取路由信息，接着找到自己要获取消息的Topic在哪几台Broker上，就可以跟Broker建立长连接，从里面拉取消息了。