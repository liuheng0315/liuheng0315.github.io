---
title: RocketMQ笔记 06-事务机制
date: 2020-08-27 11:57:23
categories: 消息中间件
tags:
	- RocketMQ
cover: https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=2582113194,968310573&fm=26&gp=0.jpg
typora-root-url: ..
---

## RocketMQ事务机制

### 基于half消息的事务机制

​	<img src="/images/half%E6%B6%88%E6%81%AF%E4%BA%8B%E5%8A%A1%E6%9C%BA%E5%88%B6.png" alt="half消息事务机制" style="zoom:50%;" />

​	生产者如订单系统在发送消息到MQ时，会先发送half消息到MQ，half消息成功后订单系统会收到half发送成功的通知，此时说明MQ存活，订单系统可以执行订单更新等本地事务，如果订单系统的本地事务执行失败可以发送一个rollback请求到MQ，告诉MQ删除之前发的half消息。如果订单系统本地事务执行成功，会发送一个commit请求给MQ进行commit操作，消息被commit之后，消费者才能看见这条消息



**如果发送half消息成功了，但是没有收到响应怎么办？**

​	half消息发送给MQ，MQ保存成功，但是由于网络等原因造成订单系统没有收到MQ返回的响应，此时订单系统会误以为发送给MQ失败了，会将订单系统标记为关闭等操作，但是这个时候MQ已经存储了一条half消息，此时由于RocketMQ存在一个补偿机制，MQ会定时扫描自己处于half状态的消息，如果一直没有对这个消息进行commit或者rollback操作，超过一定的时间，就会回调订单系统的接口，让订单系统确认是要commit还是rollback消息，如果订单系统发现订单状态是已关闭，那必然会发送rollback请求给MQ去删除half消息



**如果生产者进行commit或者rollback失败会怎么样？**

​	当half消息写入MQ成功，并且订单系统收到响应了，此时订单系统执行本地事务成功或者失败了，执行了commit或者rollback请求，发送给MQ，结果因为网络故障，导致commit或者rollback请求发送失败，此时RocketMQ后台发现一个half长时间未执行commit或者rollback操作也会执行补偿机制，回调订单系统的接口



### half消息是如何对消费者不可见及消息确认机制

​	<img src="/images/half%E6%B6%88%E6%81%AF%E7%A1%AE%E8%AE%A4%E6%9C%BA%E5%88%B6.png" alt="half消息确认机制" style="zoom:50%;" />

​	举个例子，订单系统发送了一个half状态的订单支付消息到“OrderPaySuccessTopic”里去，OrderPaySuccessTopic是一个Topic，从之前的底层原理剖析的环节知道写入一个Topic，最终是定位到这个Topic的某个MessageQueue，然后定位到一台Broker机器上去，然后写入的是Broker上的CommitLog文件，同时将消费索引写入MessageQueue对应的ConsumeQueue文件，按理说如果你写入一条half消息到OrderPaySuccessTopic里去，会定位到这个Topic的一个MessageQueue，然后定位到上图RocketMQ的一台机器上去，消息会写入CommitLog，同时消息的offset会写入MessageQueue对应的ConsumeQueue，这个ConsumeQueue是属于OrderPaySuccuessTopic的，然后消费者按理说会从这个ConsumeQueue里获取到你写入的这个half消息。但是实际上消费者却没法看到这条消息，其本质原因就是RocketMQ一旦发现你发送的是一个half消息，他不会把这个half消息的offset写入OrderPaySuccessTopic的ConsumeQueue里去,会把这条half消息写入到自己内部的**RMQ_SYS_TRANS_HALF_TOPIC**这个Topic对应的一个ConsumeQueue里去，所以消费者自然无法从OrderPaySuccessTopic的ConsumeQueue中看到这条half消息了

**生产者什么时候会收到half消息成功的响应？**

​	当half消息进入到RocketMQ内部的RMQ_SYS_TRANS_HALF_TOPIC的ConsumeQueue文件了，此时就会认为half消息写入成功了，然后就会返回响应给生产者。

**一直没有执行rollback或者commit会怎么样？**

​	假如因为网络故障，订单系统没收到half消息的响应，或者说自己发送的rollback/commit请求失败了，那么RocketMQ会干什么？

​	其实这个时候他会在后台有定时任务，定时任务会去扫描RMQ_SYS_TRANS_HALF_TOPIC中的half消息，如果你超过一定时间还是half消息，他会回调订单系统的接口，让你判断这个half消息是要rollback还是commit

**如果执行rollback操作的话，如何标记消息回滚？**

​	假设生产者执行了rollback请求，那么此时就需要对消息进行回滚，但是消息并不会真的把half消息从磁盘中删除，因为RocketMQ都是将**消息顺序写入**磁盘文件的，所以在这里如果执行rollback，他的本质就是用一个OP操作来标记half消息的状态，RocketMQ内部有一个OP_TOPIC，此时可以写一条rollback OP记录到这个Topic里，标记某个half消息是rollback了，假设你一直没有执行commit/rollback，RocketMQ会回调生产者的接口去判断half消息的状态，但是最多就是回调15次，如果15次之后都没法告知他half消息的状态，就自动把消息标记为rollback。

**如果执行commit操作，如何让消息对红包系统可见？**

​	如果执行commit操作之后，RocketMQ就会在OP_TOPIC里写入一条记录，标记half消息已经是commit状态了，接着把放在RMQ_SYS_TRANS_HALF_TOPIC中的half消息给写入到OrderPaySuccessTopic的ConsumeQueue里去，然后消费者就可以看到这条消息进行消费