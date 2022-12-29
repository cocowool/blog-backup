---
title: Kubernetes基础 Node
date: 2021-02-08 18:45:18
tags:
keywords: node, kubernetes, node controller
description: Node节点是Kubernetes集群中的计算节点，之前叫做 Minion。Node 可以是虚拟机，也可以是物理机，Node上出了Kubernetes必备的几个组件，其余的资源都用来运行Pod实例。
---

## 1. Node 是什么
Node节点是Kubernetes集群中的计算节点，之前叫做```Minion```。Node 可以是虚拟机，也可以是物理机，Node上出了Kubernetes必备的几个组件，其余的资源都用来运行Pod实例。

## 2. Node 上都有那些组件
除了操作系统之外，Node上必须具有以下几个组件
- Container Runtime
- Kubelet
- Kube-proxy

## 3. 节点的控制策略
### 3.1 Node Controller
- 分配CIDR段
- 在云环境中，保持内部节点列表与云供应商提供的节点列表状态一致，如果发现节点异常，通过查询云供应商接口决定是否移除节点
- 监控节点状态

会决定节点调度的一些参数
- node-monitor-period node controller检查node状态的间隔
- node-monitor-grace-period 默认是40s，如果node controller超过这个时间后还是无法联系上node，会将node状态设置为```Unknown```
- pod-eviction-timeout 默认是5分钟，如果node保持在```Unknown```或```False```状态超过参数设置，那么在本节点上的所有Pod会被删除并迁移
- node-eviction-rate 默认为0.1，表示每10秒驱逐一个节点
- unhealthy-zone-threshold 默认为0.55，决定Node节点的驱逐速度，当集群中不健康的节点达到阈值时，会停止驱逐节点或降低到下面参数设置的速度
- secondary-node-eviction-rate 默认为0.01，表示每100秒驱逐一个节点
- large-cluster-size-threshold 大型集群的节点规模，默认为50，小于这个数字认为是small规模的集群
- node-eviction-rate

### 3.2 节点的容量
节点的容量是Node对象的一部分，通常节点在注册的时候上报自己的容量，在使用手工方式管理时，需要手工配置容量。

## 4. 集群中 Node 的管理
与Pod、Service这些对象不一样，Node本身不是Kubernetes创建的，而是由外部的云供应商或者我们的基础设施团队提供的。当在集群中创建一个Node的时候，仅仅是在集群中创建了一个代表Node的对象。
### 3.1 增加Node节点
通过控制kubelet的```--register-node```参数，可以使用自动注册或者手工的方式来向集群中加入节点。

### 3.2 删除Node节点
```sh
kubectl delete node <node-name>
```


### 3.3 Node的隔离与恢复
在Node节点硬件需要更换的情况下，需要暂时将Node从集群中隔离开。

将Node设置为不可调度，然后将Node上的容器驱离，最后再恢复Node。
```sh
kubectl cordon <node-name>
kubectl drain <node-name>
kubectl uncordon <node-name>
```
> DaemonSet会忽视这个属性。

## 5. Node的日常维护
### 5.1 查看Node 状态
- Addresses 地址
    - HostName 主机名
    - ExternalIP 外部IP
    - InternalIP 内部IP
- Condition 状态
    - OutOfDisk
    - Ready
    - MemoryPressure
    - PIDPressure
    - DiskPressure
    - NetworkUnavailable
    - ConfigOK
- Capacity 容量，节点的CPU、内存和可调度Pod数量的状态
- Info 信息，显示节点的内核版本，Kubernetes版本，Docker版本

> 在1.5版本之前，如果node无法访问，会强制删除node上的pods。1.5版本之后，不会强制删除，只有重新连上node后才会确认是否删除，在此之前pods一直保持```Terminating```或者```Unknown```状态

## Master与Node间的通信

- Node to Master
- Master to Node
    - apiserver to kubelet
    - apiserver to nodes, pods, services

## 参考资料
1. [Kubernetes concept nodes](https://kubernetes.io/docs/concepts/architecture/nodes/)
2. [Kubernetes node 管理](https://www.cnblogs.com/breezey/p/8849472.html)
3. [手动安装k8s 但kubelet.service启动失败](https://blog.csdn.net/yangqinjiang/article/details/81538539)