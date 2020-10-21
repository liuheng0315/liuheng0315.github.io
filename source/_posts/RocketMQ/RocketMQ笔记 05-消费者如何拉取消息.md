---
title: RocketMQ笔记 05-消费者如何拉取消息
date: 2020-08-27 10:59:26
categories: 消息中间件
tags:
	- RocketMQ
cover: https://ss3.bdstatic.com/70cFv8Sh_Q1YnxGkpoWK1HF6hhy/it/u=2582113194,968310573&fm=26&gp=0.jpg
typora-root-url: ../..
---

## 消费者如何从Master Broker或者Slaver Broker上拉取数据

<img src="/images/%E6%B6%88%E8%B4%B9%E8%80%85%E6%8B%89%E5%8F%96%E6%B6%88%E6%81%AF.png" alt="消费者拉取消息" style="zoom: 50%;" />

**ConsumerQueue文件基于os cache**

​	ConsumeQueue会被大量的消费者发送的请求给高并发的读取，所有ConsumeQueue文件的读操作非常频繁，同时也会极大影响消费者进行消息的拉取的性能和消费的吞吐量，所以Broker对ConsumeQueue文件也是基于os cache进行优化，对于Broker大量的ConsumeQueue文件，在写入的时候是优先写入os cache中，而且os也有自己的优化机制，就是在读取一个磁盘文件的时候，会自动把磁盘文件的一些数据缓存到os cache中，由于ConsumeQueue文件主要是存放消息的offset，所以每个文件很小，30万条消息的offset就只有5.72M左右，完全可以被os缓存到内存cache中



**CommitLog是基于os cache+磁盘一起读取的**

​	os cache是机器的内存，一般最多也就几十个G而已，对于CommitLog而言，无法将全部数据加载到内存中，os cache主要是提升文件的写入性能，os会自动把旧数据刷入磁盘中，腾出空间给新写入的数据。

​	结论当你拉取消息的时候，可以轻松从os cache里读取少量的ConsumeQueue文件里的offset，这个性能是极高的，但是当你去CommitLog文件里读取完整消息数据的时候，会有两种可能

- 如果你读取的是那种刚刚写入CommitLog的数据，那么大概率他们还停留在os cache中，此时你可以顺利的直接从os cache里读取CommitLog中的数据，这个就是内存读取，性能是很高的
- 你也许读取的是比较早之前写入CommitLog的数据，那些数据早就被刷入磁盘了，已经不在os cache里了，那么此时你就只能从磁盘上的文件里读取了，这个性能是比较差一些的。



**什么时候会从os cache读？什么时候会从磁盘读？**

​	如果你的消费者机器一直快速的在拉取和消费处理，紧紧的跟上了生产者写入broker的消息速率，那么你每次拉取几乎都是在拉取最近人家刚写入CommitLog的数据，那几乎都在os cache里。但是如果broker的负载很高，导致你拉取消息的速度很慢，或者是你自己的消费者机器拉取到一批消息之后处理的时候性能很低，处理的速度很慢，这都会导致你跟不上生产者写入的速率，此时可能很大概率从磁盘上读取数据。



**Master Broker什么时候会让你从Slave Broker拉取数据？**

​	Broker自己是知道机器上当前的整体物理内存有多大的，而且他也知道自己可用的最大空间占里面的比例，他是知道自己的消息最多可以在内存里放多少的！比如他心知肚明，他最多存放10GB数据在os cache中，也就大概在内存里放5万条消息而已！

​	然后这个时候消费者过来拉取消息，他发现你还有8万条消息没有拉取，这个8万条消息他发现是10GB内存最多存放的5万条消息的，那么此时就说明，肯定有3万条消息目前是在磁盘上的，不在os cache内存里！

​	所以os cache经过上述判断，会发现此时你很大概率会从磁盘里加载3万条消息出来！他会认为，出现这种情况，很可能是因为自己作为master broker负载太高了，导致没法及时的把消息给你，所以你落后的进度比较多。这个时候，Master Broker就会告诉消费者，这次给你从磁盘里读取3万条消息，但是下次你还是从slave broker去拉取吧！

​	本质是对比消费者当前没有拉取消息的数量和大小，以及最多可以存放在os cache内存里的消息的大小，如果你没拉取的消息超过了最大能使用的内存的量，那么说明消费者后续会频繁从磁盘加载数据，此时就让消费者从slave broker去加载数据了！

