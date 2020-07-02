---
title: Kubernetes中的亲和性与反亲和性
date: 2018-09-29 13:30
tag: 
---

通常情况下，Pod分配到哪些Node是不需要管理员操心的，这个过程会由scheduler自动实现。但有时，我们需要指定一些调度的限制，例如某些应用应该跑在具有SSD存储的节点上，有些应用应该跑在同一个节点上等等。
> 截止到Kubernetes 1.11版本，节点亲和性的特性还是Beta阶段。


## nodeSelector
首先我们为Node规划标签，然后在创建部署的时候，通过使用```nodeSelector```标签来指定Pod运行在哪些节点上。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    env: test
spec:
  containers:
  - name: nginx
    image: docker.io/nginx
    imagePullPolicy: IfNotPresent
  nodeSelector:
    disktype: ssd
```
> nodeSelector 将会在后续的版本中废除，还是建议大家使用使用亲和性策略。


## 亲和与反亲和 Affinity and anti-affinity
nodeSelector的调度方式略显简单，通过亲和和反亲和配置，能够为调度提供更灵活的策略，主要有以下几点增强：

* 更多的表达式支持，不仅仅是ADD和精确匹配了
* 可以设置soft/preference的调度策略，而不是刚性的要求
* 可以通过Pod的标签进行调度约束，不仅仅是Node的标签
亲和性特性包含两种方式

### 节点亲和性 Node affinity
Node affinity 是 Kubernetes 1.2版本后引入的新特性，类似于nodeSelector，允许我们指定一些Pod在Node间调度的约束。支持两种形式：```requiredDuringSchedulingIgnoredDuringExecution```和```preferredDuringSchedulingIgnoredDuringExecution```，可以认为前一种是必须满足，如果不满足则不进行调度，后一种是倾向满足，不满足的情况下会调度的不符合条件的Node上。```IgnoreDuringExecution```表示如果在Pod运行期间Node的标签发生变化，导致亲和性策略不能满足，则继续运行当前的Pod。
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
spec:
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/e2e-az-name
            operator: In
            values:
            - e2e-az1
            - e2e-az2
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 1               //取值范围1-100
        preference:
          matchExpressions:
          - key: another-node-label-key
            operator: In
            values:
            - another-node-label-value
  containers:
  - name: nginx
    image: docker.io/nginx
```
标签判断的操作符除了使用```In```之外，还可以使用```NotIn```、```Exists```、```DoesNotExist```、```Gt```、```Lt```。如果指定多个```nodeSelectorTerms```，则只要满足其中一个条件，就会被调度到相应的节点上。如果指定多个```matchExpressions```，则所有的条件都必须满足才会调度到对应的节点。

### Pod间的亲和性与反亲和性 inter-pod affinity/anti-affinity
这个特性是Kubernetes 1.4后增加的，允许用户通过已经运行的Pod上的标签来决定调度策略，用文字描述就是“如果Node X上运行了一个或多个满足Y条件的Pod，那么这个Pod在Node应该运行在Pod X”，因为Node没有命名空间，Pod有命名空间，这样就允许管理员在配置的时候指定这个亲和性策略适用于哪个命名空间，可以通过```topologyKey```来指定。topology是一个范围的概念，可以是一个Node、一个机柜、一个机房或者是一个区域（如北美、亚洲）等，实际上对应的还是Node上的标签。
有两种类型

* requiredDuringSchedulingIgnoredDuringExecution，刚性要求，必须精确匹配
* preferredDuringSchedulingIgnoredDuringExecution，软性要求
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: with-pod-affinity
spec:
  affinity:
    podAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
      - labelSelector:
          matchExpressions:
          - key: security
            operator: In
            values:
            - S1
        topologyKey: failure-domain.beta.kubernetes.io/zone
    podAntiAffinity:
      preferredDuringSchedulingIgnoredDuringExecution:
      - weight: 100
        podAffinityTerm:
          labelSelector:
            matchExpressions:
            - key: security
              operator: In
              values:
              - S2
          topologyKey: kubernetes.io/hostname
  containers:
  - name: with-pod-affinity
    image: k8s.gcr.io/pause:2.0
```
标签的判断操作支持```In```、```NotIn```、```Exists```、```DoesNotExist```。
原则上```topologyKey```可以是节点的合法标签，但是有一些约束：

* 对于亲和性以及RequiredDuringScheduling的反亲和性，topologyKey需要指定
* 对于RequiredDuringScheduling的反亲和性，LimitPodHardAntiAffinityTopology的准入控制限制topologyKey为kubernetes.io/hostname,可以通过修改或者disable解除该约束
* 对于PreferredDuringScheduling的反亲和性，空的topologyKey表示kubernetes.io/hostname, failure-domain.beta.kubernetes.io/zone and failure-domain.beta.kubernetes.io/region的组合．
* topologyKey在遵循其他约束的基础上可以设置成其他的key.
---本文来自 阿仆来耶 的CSDN 博客 ，全文地址请点击：[https://blog.csdn.net/jettery/article/details/79003562?utm_source=copy](https://blog.csdn.net/jettery/article/details/79003562?utm_source=copy)
> Pod间的亲和性策略要求可观的计算量可能显著降低集群的性能，不建议在超过100台节点的范围内使用。
Pod间的反亲和策略要求所有的Node都有一致的标签，例如集群中所有节点都应有匹配topologyKey的标签，如果一些节点缺失这些标签可能导致异常行为。


## 常用场景
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: redis-cache
spec:
  selector:
    matchLabels:
      app: store
  replicas: 3
  template:
    metadata:
      labels:
        app: store
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: redis-server
        image: redis:3.2-alpine
```
上面的例子中，创建了一个具有三个实例的部署，采用了Pod间的反亲和策略，限制创建的实例的时候，如果节点上已经存在具有相同标签的实例，则不进行部署，避免了一个节点上部署多个相同的实例。
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: web-server
spec:
  selector:
    matchLabels:
      app: web-store
  replicas: 3
  template:
    metadata:
      labels:
        app: web-store
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - web-store
            topologyKey: "kubernetes.io/hostname"
        podAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: app
                operator: In
                values:
                - store
            topologyKey: "kubernetes.io/hostname"
      containers:
      - name: web-app
        image: nginx:1.12-alpine
```
再创建3个Web服务的实例，同上面Redis的配置，首先确保两个Web不会部署到相同的节点，然后在应用Pod间亲和策略，优先在有Redis服务的节点上部署Web。

## 参考资料

1. [Kubernetes中的亲和性](https://blog.csdn.net/jettery/article/details/79003562)
2. [Assigning Pods to Nodes](https://kubernetes.io/docs/concepts/configuration/assign-pod-node/)












