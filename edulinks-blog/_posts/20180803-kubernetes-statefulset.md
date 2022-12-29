---
title: Kubernetes中StatefulSet介绍
date: 2018-08-02 16:06
tag: 
keywords: kubernetes, k8s, statefulset
description: StatefulSet是Kubernetes提供的管理有状态应用的负载管理控制器API。在Pods管理的基础上，保证Pods的顺序和一致性。
---

> StatefulSet 是Kubernetes1.9版本中稳定的特性，本文使用的环境为 Kubernetes 1.11。如何搭建环境可以参考[kubeadm安装kubernetes V1.11.1 集群](https://www.edulinks.cn/2018/07/24/20180724-kubeadm-install-kubernetes/) 。想要了解更多 Kubernetes 相关知识，可以阅读 [Kubernetes 系列学习文章](http://www.edulinks.cn/2020/10/16/20201016-kubernetes-articles/)。


## 0. 介绍
使用Kubernetes来调度无状态的应用非常简单，那Kubernetes如何来管理调度有状态的应用呢？Kubernetes中提供了一个StatefulSet控制器来管理有状态的应用，本文就介绍StatefulSet的应用，解决以下几个问题：

* 如何创建StatefulSet
* StatefulSet如何管理Pods
* 如何删除StatefulSet
* 如何对StatefulSet进行扩容
* 如何更新StatefulSet中的Pods

## 1. StatefulSet 是什么
StatefulSet是Kubernetes提供的管理有状态应用的负载管理控制器API。在Pods管理的基础上，保证Pods的顺序和一致性。与Deployment一样，StatefulSet也是使用容器的Spec来创建Pod，与之不同StatefulSet创建的Pods在生命周期中会保持持久的标记（例如Pod Name）。
StatefulSet适用于具有以下特点的应用：

* 具有固定的网络标记（主机名）
* 具有持久化存储
* 需要按顺序部署和扩展
* 需要按顺序终止及删除
* 需要按顺序滚动更新

## 2. StatefulSet 创建
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  ports:
  - port: 80
    name: web
  clusterIP: None
  selector:
    app: nginx
---
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: web
spec:
  serviceName: "nginx"
  replicas: 2
  selector:
     matchLabels:
       app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: docker.io/nginx
        ports:
        - containerPort: 80
          name: web
        volumeMounts:
        - name: www
          mountPath: /usr/share/nginx/html
  volumeClaimTemplates:
  - metadata:
      name: www
    spec:
      accessModes: ["ReadWriteOnce"]
      volumeMode: Filesystem
      resources:
        requests:
          storage: 50Mi
      storageClassName: local-storage
```
StatefulSet创建顺序是从0到N-1，终止顺序则是相反。如果需要对StatefulSet扩容，则之前的N个Pod必须已经存在。如果要终止一个Pod，则它的后序Pod必须全部终止。
> If web-0 should fail, after web-1 is Running and Ready, but before web-2 is launched, web-2 will not be launched until web-0 is successfully relaunched and becomes Running and Ready.

> 在Kubernetes 1.7版本后，放松了顺序的保证策略，对应的参数为 .spec.podManagementPolicy

执行创建命令，并且观察对象是否正常创建。
```bash
[root@devops-101 ~]# kubectl create -f ss-nginx.yml 
service "nginx" created
statefulset "web" created
[root@devops-101 ~]# kubectl get svc
NAME         TYPE        CLUSTER-IP   EXTERNAL-IP   PORT(S)   AGE
kubernetes   ClusterIP   10.96.0.1    <none>        443/TCP   9d
nginx        ClusterIP   None         <none>        80/TCP    1d
[root@devops-101 ~]# kubectl get statefulset 
NAME      DESIRED   CURRENT   AGE
web       2         2         1d
```
看一下Pod是否是有顺序的
```bash
[root@devops-101 ~]# kubectl get pods -l app=nginx
NAME      READY     STATUS    RESTARTS   AGE
web-0     1/1       Running   0          11m
web-1     1/1       Running   0          11m
```
根据Pod的顺序，每个Pod拥有对应的主机名，在Pod中执行```hostname```命令确认下。
```bash
[root@devops-101 ~]# for i in 0 1; do kubectl exec web-$i -- sh -c 'hostname'; done
web-0
web-1
```
使用带有nslookup命令的busybox镜像启动一个Pod，检查集群内的DNS地址设置。

## 3. 扩缩容
将Pod实例扩充到5个。
```yaml
[root@devops-101 ~]# kubectl scale sts web --replicas=5
statefulset.apps/web scaled
[root@devops-101 ~]# kubectl get pods -w
NAME      READY     STATUS    RESTARTS   AGE
web-0     1/1       Running   0          5m
web-1     1/1       Running   0          5m
web-2     0/1       Pending   0         1s
web-2     0/1       Pending   0         1s
```
因为我的集群PV不是动态供给的，所以扩容停留在等待PV的阶段，本文要按照后面的办法手工创建相应的PV。
将实例减少到2个。
```yaml
[root@devops-101 ~]# kubectl get pod
NAME      READY     STATUS    RESTARTS   AGE
web-0     1/1       Running   0          24m
web-1     1/1       Running   0          23m
web-2     1/1       Running   0          18m
web-3     1/1       Running   0          10m
web-4     1/1       Running   0          6m
[root@devops-101 ~]# kubectl patch sts web -p '{"spec":{"replicas":2}}'
statefulset.apps/web patched
```

## 4. 更新策略

### 4.1 滚动更新
默认的更新策略，以相反的顺序依次更新Pod

### 4.2 Partition

### 4.3 金丝雀发布 Canary
金丝雀发布。

## 5. 删除 StatefulSet
StatefulSet支持级连删除和非级连删除，在非级连删除模式下，仅删除StatefulSet不删除Pod，级连删除则全部删除。
非级连删除StatefulSet后，如果删除Pod，就不会重新拉起原来的Pod，而是新建一个Pod。但是如果重新创建StatefulSet，则会对现有的Pod按照规则进行重新整理。
一些分布式系统，并不希望按照顺序来管理启停Pod，因此在1.7版本之后，提供了```.spec.podManagementPolicy```这个参数，默认为```OrderedReady```，可以设置为```Parallel```这样Pod的创建就不必等待，而是会同时创建、同时删除。

## X. 坑
官方的文档没有创建PVC对应的PV，按照官方文档操作创建后会遇到下面的错误，需要提前创建PV及PVC。关于存储的更详细的内容，可以参考[Kubernetes 存储系统 Storage 介绍](https://www.cnblogs.com/cocowool/p/kubernetes_storage.html)。
> pod has unbound PersistentVolumeClaims

官方文档里解释了，因为他所用的集群配置为动态提供PV，所以不用手工创建。
> As the cluster used in this tutorial is configured to dynamically provision PersistentVolumes, the PersistentVolumes were created and bound automatically.

PV的创建脚本如下：
```yaml
kind: List
apiVersion: v1
items:
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: es-storage-pv-01
  spec:
    capacity:
      storage: 100Mi
    volumeMode: Filesystem
    accessModes: ["ReadWriteOnce"]
    persistentVolumeReclaimPolicy: Delete
    storageClassName: local-storage
    local:
      path: /home/es
    nodeAffinity:
      required:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/hostname
            operator: In
            values:
            - devops-102
            - devops-103
- apiVersion: v1
  kind: PersistentVolume
  metadata:
    name: es-storage-pv-02
  spec:
    capacity:
      storage: 100Mi
    volumeMode: Filesystem
    accessModes: ["ReadWriteOnce"]
    persistentVolumeReclaimPolicy: Delete
    storageClassName: local-storage
    local:
      path: /home/es01
    nodeAffinity:
      required:
        nodeSelectorTerms:
        - matchExpressions:
          - key: kubernetes.io/hostname
            operator: In
            values:
            - devops-102
            - devops-103
```


## 参考资料

1. [StatefulSet Basics](https://kubernetes.io/docs/tutorials/stateful-application/basic-stateful-set/)
2. [DNS for Services and Pods](https://kubernetes.io/docs/concepts/services-networking/dns-pod-service/)
3. [Services](https://kubernetes.io/docs/concepts/services-networking/service/#headless-services)
4. [Persistent Volumes](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
5. [PersistentVolume Provisioning](https://github.com/kubernetes/examples/tree/master/staging/persistent-volume-provisioning/)
6. [StatefulSet Concept](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/)












