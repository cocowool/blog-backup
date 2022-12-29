---
title: Kafka学习笔记三：常用参数以及调优配置
date: 2021-09-15 22:35:26
tags:
keywords: Kafka, Kafka 调优
description: 本文介绍Kafka的参数和调优配置。
---

## 参数

### Consumer 参数

| 名称 | 描述 |
| --- | --- |
| bootstrap.servers | Kafka集群初始化连接的服务器列表。 |
| group.id | 表示消费者组的唯一标识。This property is required if the consumer uses either the group management functionality by using subscribe(topic) or the Kafka-based offset management strategy。 |
| heartbeat.interval.ms | 期待的到consumer coordinator的心跳时间。心跳确保消费者的会话保持存活，以便在有新的消费者加入或离开时进行rebalance。这个值必须小于`session.timeout.ms`，但一般不应高于其值的三分之一。 |
| zookeeper.connect |指定zookeeper的连接的字符串，格式是 `hostname:port` ，此处 host 和 port 都是 zookeeper server 的host 和 port，为避免某个 zookeeper 机器宕机之后失联，你可以指定多个 `hostname:port`，使用逗号作为分隔：`hostname1:port1, hostname2:port2, hostname3:port3` 可以在zookeeper连接字符串中加入zookeeper的chroot路径，此路径用于存放他自己的数据，方式：`hostname1:port1, hostname2:port2, hostname3:port3/chroot/path` |
| socket.timeout.ms |默认为 30*100，网络请求的超时限制。真实的超时限制是  max.fetch.wait + socket.timeout.ms |
| socket.receive.buffer.bytes |默认为 64*1024，设置 socket 用于接收网络请求的缓存大小。 |
| fetch.message.max.bytes |默认为 1024*1024，每次fetch请求中，针对每次fetch消息的最大字节数。这些字节将会督导用于每个partition的内存中，因此，此设置将会控制consumer所使用的memory大小。这个fetch请求尺寸必须至少和server允许的最大消息尺寸相等，否则，producer可能发送的消息尺寸大于consumer所能消耗的尺寸。 |
| num.consumer.fetchers |默认为 1，用于 fetch 数据的 fetcher 线程数。 |
| auto.commit.enable | 默认为 true，表示 consumer 所 fetch 的消息的 offset 将会自动同步到 zookeeper 。这项提交的 offset 将在进程挂掉时，由新的 consumer 使用。 |
| auto.commit.interval.ms | 默认为 60*1000，consumer 向 zookeeper 提交 offset 的频率，单位是秒。 |
| queued.max.message.chunks | 默认为 2，表示用于缓存消息的最大数目，以供 consumption。每个 chunk 必须和fetch.message.max.bytes 相同。 |
| rebalance.max.retries | 默认为 4。当新的 consumer 加入到 consumer  group 时，consumers 集合试图重新平衡分配到每个 consumer 的 partitions 数目。如果 consumers 集合改变了，当分配正在执行时，这个重新平衡会失败并重入。 |
| fetch.min.bytes | 默认为 1，表示每次 fetch 请求时，server 应该返回的最小字节数。如果没有足够的数据返回，请求会等待，直到足够的数据才会返回。 |
| fetch.wait.max.ms | 默认为 100，表示如果没有足够的数据能够满足 fetch.min.bytes，则此项配置是指在应答 fetch 请求之前，server会阻塞的最大时间。 |
| rebalance.backoff.ms | 默认为 2000，表示在重试 reblance 之前 backoff 的时间。 |
| refresh.leader.backoff.ms | 默认为 200，表示在试图确定某个 partition 的 leader 是否失去他的 leader 地位之前，需要等待的 backoff 的时间。 |
| auto.offset.reset | 默认为 largest。如果 zookeeper 中没有初始化的 offset 时，如果 offset 是以下值的回应：<br/>smallest：自动复位 offset 为 smallest 的 offset<br/>largest：自动复位 offset 为 largest 的 offset<br/>anything  else：向 consumer 抛出异常 |
| consumer.timeout.ms | 默认为 -1，如果没有消息可用，即使等待特定的时间之后也没有，则抛出超时异常。 |
| exclude.internal.topics | 默认为 true，表示是否将内部 topics 的消息暴露给 consumer。 |
| paritition.assignment.strategy | 默认为 range，表示选择向 consumer 流分配 partitions 的策略，可选值：range，roundrobin。 |
| client.id | 是用户特定的字符串，用来在每次请求中帮助跟踪调用。它应该可以逻辑上确认产生这个请求的应用。 |
| zookeeper.session.timeout.ms | 默认为 6000，表示 zookeeper 会话的超时限制。如果 consumer 在这段时间内没有向zookeeper 发送心跳信息，则它会被认为挂掉了，并且 reblance 将会产生。 |
| zookeeper.connection.timeout.ms | 默认为 6000，表示客户端在建立同 zookeeper 连接中的最大等待时间。 |
| zookeeper.sync.time.ms | 默认为 2000，表示 ZK follower 可以落后 ZK leader 的最大时间。 |
| offsets.storage | 默认为 zookeeper，表示用于存放 offsets 的地点： zookeeper 或者 kafka。 |
| offset.channel.backoff.ms | 默认为 1000，表示重新连接 offsets channel 或者是重试失败的 offset 的 fetch/commit 请求的 backoff 时间。 |
| offsets.channel.socket.timeout.ms | 默认为 10000，当读取offset的fetch/commit请求回应的socket 超时限制。此超时限制是被consumerMetadata请求用来请求offset管理。 |
| offsets.commit.max.retries | 默认为 5，表示重试offset commit的次数。这个重试只应用于offset  commits在shut-down之间。 |
| dual.commit.enabled | 默认为 true，如果使用“kafka”作为offsets.storage，你可以二次提交offset到zookeeper(还有一次是提交到kafka）。在zookeeper-based的offset  storage到kafka-based的offset storage迁移时，这是必须的。对任意给定的consumer  group来说，比较安全的建议是当完成迁移之后就关闭这个选项。 |



### Broker

| 名称 | 描述 |
| --- | --- |
| auto.create.topics.enable | true 表示允许消费者自动创建不存在的topic |
| delete.topic.enable | true 删除topic的时候同时进行物理删除 |

## 调优
Kafka调优大概分为几个方面：

* Producer 
    * 压缩率
    * 批次大小
    * 同步或异步
* Consumer 
    * 每次获取数据的大小
* Broker

看下图可以了解到，kafka调优是一个平衡的过程，如果希望获得大的消息处理能力，那么可能造成延时的提高。因此，我们要根据应用场景的实际需要，选择一个最佳的平衡点。
![06458b6140e7583ba12afd50d454b600.png](evernotecid://ABDCA1EB-5F78-4410-93E7-50133663A78D/appyinxiangcom/28528220/ENResource/p154)

### 评价指标
* Latency 处理一条消息所需要的时间
* Throughput 表示特定时间内处理消息的条数

总结一下，对于一个调整好的Kafka集群来说，有足够的broker来提供Topic的处理能力，同时保证消息的处理延时维持在一定的水平之下。

### Producer 调优
Kafka使用异步的`发布订阅模型`。当Producer调用`send()`方法时，结果会在未来返回。对于Broker来说则是等待事件，接收数据，处理完成后发送处理结果。

### Batch Size
与消息条数不同，`batch.size`从总的字节水平衡量批次的大小，这个参数控制发送给Broker之前，每次可以收集的数据大小。这个参数的默认值是`16384`，在内存足够的情况下，应该是越大越好。

### Linger Time
`linger.ms`设置可以缓存数据的最大时间。这个值设的大了，Throughput会变大，同时Latency也会增加。

### Brokder 调优
Topic分为多个Partition，每个Partition有一个Leader，多个副本，多数情况下数据都是写入Leader。如果数据负载均衡做的不好，将会造成个别点特别忙，从而影响整体性能。

推荐一个物理磁盘对应一个分区，一个Consumer对应一个分区。

* `num.replica.fetchers` 从leader拉取数据的replica线程数。配置高可以提高并发度，单位时间内leader处理请求增加，负载会增加。
* `replica.fetch.max.bytes` 每次请求从分区获取数据的大小
* `replica.fetch.wait.max.ms` 
* `replica.socket.receive.buffer.bytes` 
* `num.partitions` 分区数量取决于可用的线程数和磁盘数量
* `num.io.threads` Broker处理磁盘IO的线程数量，线程的数量最好与磁盘数量一致。也有朋友建议高峰期io会有等待，建议配置为CPU核数2倍，最大不超过3倍。
* `num.network.threads` Broker处理消息的最大线程数

### Consumer 调优
Consumer的数量应该与Topic分区的数量一致。

* `replica.high.watermark.checkpoint.interval.ms`参数会影响Throughput。如果为每次消费都进行标注，会产生明显的性能影响。

## Benchmarking 
Kafka提供了一个用于

## 参考资料
1. [Kafka 010 document](http://kafka.apache.org/0100/documentation.html)
2. [大数据_Kafka_Kafka自动创建不存在的Topics / 删除已存在的Topics](https://blog.csdn.net/u010003835/article/details/52790378)
2. [Kafka性能测试实例](https://www.cnblogs.com/smartloli/p/10093838.html)
3. [kafka性能测试](https://www.cnblogs.com/runnerjack/p/9105784.html)
4. [Kafka Performance Tuning – Ways for Kafka Optimization](https://data-flair.training/blogs/kafka-performance-tuning/)
5. [关于Kafka部署优化的一点建议](https://www.cnblogs.com/senlinyang/p/8143268.html)
6. [Benchmarking Apache Kafka: 2 Million Writes Per Second ](https://engineering.linkedin.com/kafka/benchmarking-apache-kafka-2-million-writes-second-three-cheap-machines)