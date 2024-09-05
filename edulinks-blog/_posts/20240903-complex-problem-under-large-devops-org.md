---
title: 大型运维组织中的问题排查困境
date: 2024-09-03 18:24:39
keywords: 问题排查, 运维问题排查, 生产故障, 故障排查, 故障定位, 线上问题排查
description: 
---

笔者最近经历了一起生产系统线上问题处置、排查的过程，因为涉及业务应用、大数据平台、操作系统、硬件、厂商、网络几个团队，在寻找根本问题的过程中虽然费劲了心力，但仍得不到令人满意的结论，本文将相关过程记录下来并尝试分析深层次的问题和解决办法。

## 排查始末

有一个总容量在1PB左右的ES集群，周六早上的时候监控发现集群的写入性能下降明显，大量的业务数据已经开始排队等待写入产生了堆积。整个集群设备数量有50多台，ES节点数量有200多个。首先是应用运维的同事收到业务告警开始排查，同时也联系了大数据负责ES的同事协助排查。

当时，从集群的健康度和节点状态上看，整个集群还是 Green 状态，没有发生某个ES节点的重启。集群的写入也并非完全停止，排查ES集群的慢查询语句也并没有发现大量的查询，排除因为大量查询引起的集群性能下降。

在这期间，分别联系了网络、环境、操作系统领域的同事分别检查是否有相关的故障信息。网络领域排查网络上没问题，但是观察到某些设备有网络流入流量的突增和突降。环境领域排查服务器硬件层面没有告警。操作系统领域通过 iostat、sar 等命令检查，认为当时磁盘高的现象都是应用正常使用导致，操作系统层面并无异常。

因为应用运维人员没有ES集群的全部权限，不能查看ES日志，后续的排查更多的以大数据的同事为主，大概花了40多分钟后定位到集群中有两台服务器的写入任务堆积比较多，同时发现这两台设备的磁盘写入速度不高，但是 util 使用率几乎被打满，怀疑是磁盘故障，决定对这两台机器进行隔离处置。

隔离处置的操作很快，但是因为这些服务器中数据量比较大，大数据服务器单台20T空间，使用率均在70%以上，数据迁移用了很长时间，大概花了2个多小时，整个集群最后恢复到了正常状态。

## 问题定位

生产的故障已经解除，最后对于问题定位有了困难。大数据团队认为是磁盘有问题，慢盘影响和拖累了整个集群。

而环境团队认为磁盘硬件上并无明显报错，一致性校验任务也是一个固定的检查动作，需要上层应用分析为何应用会受到影响。环境团队答复一致性校验是在操作系统中配置的任务，可以根据应用需求进行调整。

我们作为应用运维团队，真是左右为难，根本原因没有一方愿意承担，后续措施不好具体落实，如何推进系统的安全稳定运行?

## 深层问题

因为笔者所在单位对于线上生产问题的处理和考核非常严格，这就造成了「铁路警察，各管一段」的情况。环境团队觉得磁盘一致性校验是提供了多个选项的服务，具体使用由上层来控制。大数据团队觉得基础设施的性能变化影响了应用，应该由基础设施来提供优化解决措施。

这种场景，如果对于管理模式为竖井式的团队来说，从基础设施到上层应用都由一个团队负责，则很容易达成问题点和优化的措施，推进起来就比较困难。

而我所在单位为了推进专业化分工，采用了按领域划分的管理模式，这就提高了问题解决的沟通协调成本。


## 参考资料
1. [以阿里为例，详解SRE的团队建设与职能分工](https://dbaplus.cn/news-134-2930-1.html)