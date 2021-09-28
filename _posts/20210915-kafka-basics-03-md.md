---
title: Kafka学习笔记三：常用参数以及调优配置
date: 2021-09-15 22:35:26
tags:
keywords: Kafka, Kafka 调优
description: 本文介绍Kafka的参数和调优配置。
---

## 参数

### Consumer 参数

| 名称 | 描述 | 类型 | 默认值 |  | 重要性 |
| --- | --- | --- | --- | --- | --- |
| bootstrap.servers | Kafka集群初始化连接的服务器列表。 |  |  |  | 高 |
| group.id | 表示消费者组的唯一标识。This property is required if the consumer uses either the group management functionality by using subscribe(topic) or the Kafka-based offset management strategy. | 字符串 |  |  | 高 |
| heartbeat.interval.ms | 期待的到consumer coordinator的心跳时间。心跳确保消费者的会话保持存活，以便在有新的消费者加入或离开时进行rebalance。这个值必须小于`session.timeout.ms`，但一般不应高于其值的三分之一。 | 整型 | 3000 |  | 高 |
| session.timeout.ms |  | 整型 | 10000 |  | 高 |
|  |  |  |  |  |  |

### Broker
* auto.create.topics.enable=true 允许消费者自动创建不存在的topic
* delete.topic.enable=true 删除topic的时候同时进行物理删除

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