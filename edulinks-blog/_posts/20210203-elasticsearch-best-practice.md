---
title: Elasticsearch 运维最佳实践
date: 2021-02-03 15:08:52
tags:
keywords: elasticsearch, elasticsearch 运维, elasticsearch 配置
description: Elasticsearch用于生产环境要根据具体场景对参数做具体的调整，本文整理了一部分工作积累，还将继续补充。
---

Elasticsearch用于生产环境时，需要我们根据业务场景进行不同的参数配置。本文整理了在工作过程中使用到的一些实际经验，整理出来分享给大家，后续还将不断的优化补充。

## 字段映射

进入Elasticsearch存储的内容虽然可以不用手工映射，但是在生产上可千万别这么做。在生产环境，一定要做到每个字段都映射为具体的数据类型。

## 配置参数

默认Elasticsearch节点的主机名是 **elasticsearch** ，在生产上我们通常有很多个节点，这就要求我们做好主机名的规划，建议按照 **业务\_角色\_序号** 的规则来命名你的主机名。例如存放日志的ES集群数据节点的命名规则为 **es\_log\_data\_001**。

```yaml
cluster.name: es_log_cluster
nodename: es_log_data_001
```

 合理规划分片、副本配置。


| 参数 | 描述 | 备注 |
| --- | --- | --- |
| index.number_of_shards: 5 | 分片的数量 |  |
| index.number_of_replicas: 3 | 副本的数量 |  |
| cluster.routing.allocation.disk.threshold_enabled: false | 默认为true。表示打开es的磁盘水位控制，该配置打开后，如果节点磁盘使用率超过高低水位，将会停止在该节点创建分片。 |  |

## Recovery参数

在Elasticsearch中，通过 recovery 相关参数能够在节点重启、节点故障时自动判断是否需要进行索引恢复，从而避免整个集群不可用或数据丢失的情况发生。因为 recovery 过程需要消耗额外的CPU、内存资源，如果设置不当也可能会导致集群的服务性能下降。

下面来看一下具体的参数和配置：

```yaml
# 表示集群中有多少个节点后可以开始recovery动作，第一个行包括master和data节点，后两行则分别针对master和data设置数量。
gateway.expected_nodes
gateway.expected_master_nodes
gateway.expected_data_nodes

# 表示集群在开始执行 recover 动作最长的等待时间
gateway.recover_after_time: 5m

# 表示如果集群等待超时后，当集群中有大于以下配置的节点数，即可以开始 recover
gateway.recover_after_nodes
gateway.recover_after_master_nodes
gateway.recover_after_data_nodes
```



## 其他配置优化

- 增加Refresh间隔，减少副本数量
- 加大线程池阻塞队列长度，`thread_pool.bulk.queue_size`，默认为500，可适度调整到1000-2000
- QueryCache：过滤查询过多可调大`indices.queries.cache.size`
- FieldDataCache：聚类或排序场景较多可调大`indices.fielddata.cache.size`
- bootstrap.memory_lock：这个参数的目的是当你无法关闭系统的swap的时候，建议把这个参数设为true。防止在内存不够用的时候，elasticsearch的内存被交换至交换区，导致性能骤降。bootstrap.memory_lock 的作用就是在程序启动的时候先去获取足够大的内存，再加入集群接收请求。没有 bootstrap.memory_lock 的话，elasticsearch一启动就会发现集群接入集群，接收请求，然后JVM一点点的获取系统内存，直到指定值。

## 索引优化
- 不需要过滤时可以禁用索引：`"index":false`
- 不需要text字段的score，可以禁用：`"norms":false`
- 不需要短语查询可以不索引positions：`"index_options":"freqs"`
- 禁用全文检索功能：`"_all enabled":false`

## 查询语句优化
- 尽量避免使用script，如果使用可以选择painless & experssions引擎
- 避免大量动态脚本产生，脚本需要编译才能执行
- 避免大查询或大聚合，会导致ES响应慢，并且占用JVM内存导致其他任务积压

## 运维操作步骤优化
- 同步完成后再增加副本，可以避免主副分片同步带来的压力

## 硬件升级
- 使用SSD硬盘，提高读写性能
- 使用性能更好的CPU，增大并发

## 关键检查点
### 索引
* 避免同一个Index所有shard落入一个数据节点
* 根据业务场景动态调整 `refresh_interval`
* 调整 `flush_threshold_size` 大小
* 每个Shard占用磁盘控制在10-15GB
* 同一个节点管理的分片数不要超过600个，20 shard/per gb heap
* 注意索引写入过程中的 Throttling 次数
* 根据历史统计信息，动态调整索引配置
* 在索引中定义太多字段可能会导致索引膨胀，出现内存不足和难以恢复的情况

## 参考资料：
1. [Elasticsearch分片、副本与路由(shard replica routing)](https://www.cnblogs.com/kangoroo/p/7622957.html)
2. [ES集群运维实战](https://elasticsearch.cn/slides/188#page=12)
3. [Elasticsearch 6.x Mapping设置](https://cloud.tencent.com/developer/news/300700)
4. [Elasticsearch的Best Practice：把bootstrap.mlockall设为true](https://zhaoyanblog.com/archives/826.html)
5. [Running a 400+ Node Elasticsearch Cluster](https://underthehood.meltwater.com/blog/2018/02/06/running-a-400+-node-es-cluster/)
6. [Elasticsearch high disk watermark 问题](https://my.oschina.net/u/2242064/blog/493565)