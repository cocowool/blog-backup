---
title: Kafka 高可用介绍
date: 2020-03-16 17:20:57
tags: Kafka
---



[TOC]



# 集群

# ISR机制



# 日志复制算法



# 性能问题

* kafka客户端的写性能取决于ISR集合中的最慢的一个broker的接收消息的性能，如果一个点性能太差，就必须尽快的识别出来，然后从ISR集合中踢出去，以免造成性能问题。
* 

# 参数

* replica.lag.max.messages 
* replica.lag.time.max.ms

# 参考资料

1. [图解 kafka 的高可用机制](https://www.jianshu.com/p/ff296d51385a)
2. []()