---
title: 使用kubectl创建部署
date: 2018-07-25 14:38
tag: Kubernetes
keywords: kubernetes, kubectl, docker
description: 本文介绍如何在命令行下使用 kubectl 命令创建并管理部署。
---

> 本文使用自己利用VirtubalBox搭建的集群环境，暂时只有一个Master、一个Node。如果想了解集群的搭建，可以参考我的文章[离线环境安装Kubernetes集群](https://www.cnblogs.com/cocowool/p/install_k8s_offline.html)以及[使用kubeadm安装kubernetes V1.11.1 集群](https://www.edulinks.cn/2018/07/24/20180724-kubeadm-install-kubernetes/)。

## 0. 目标

在命令行下使用 kubectl 命令创建并管理部署。

## 1. 检查环境
检查本地的环境信息。
```bash
[root@devops-101 ~]# kubectl get nodes
NAME         STATUS     ROLES     AGE       VERSION
devops-101   Ready      master    7h        v1.11.1
devops-102   Ready      <none>    6h        v1.11.1
```

## 2. 命令行方式创建部署
创建Tomcat部署，设置两个副本。
```bash
$ kubectl run docker.io/tomcat --replicas=2 --labels="app=tomcat" --image=docker.io/tomcat --port=8080
deployment.apps/tomcat created
[root@devops-101 ~]# kubectl get deployment
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
tomcat    2         2         2            2           6m
[root@devops-101 ~]# kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
tomcat-858b8c476d-vnm98   1/1       Running   0          6m
tomcat-858b8c476d-xl5xl   1/1       Running   0          6m
```
创建部署之后，可以看到容器已经运行了，但是默认情况下，容器只能内部互相访问，如果需要对外提供服务，有以下几种方式：

* ClusterIP，默认的方式，通过集群IP来对外提供服务，这种方式只能在集群内部访问。
* NodePort，利用NAT技术在Node的指定端口上提供对外服务。外部应用通过**:**的方式访问。
* LoadBalancer，利用外部的负载均衡设施进行服务的访问。
* ExternalName，这是1.7版本之后 kube-dns 提供的功能。

### 2.1 端口映射，向外部暴露服务
在Kubernetes中Pod有其自己的生命周期，Node发生故障时，ReplicationController或者ReplicationSet会将Pod迁移到其他节点中以保持用户希望的状态。
```bash
[root@devops-101 ~]# kubectl expose deployment/tomcat --type="NodePort" --port 8080
service/tomcat exposed
```
查看service被映射到哪个端口。
```bash
[root@devops-101 ~]# kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP          8h
tomcat       NodePort    10.98.152.28   <none>        8080:32050/TCP   7s
```
因为知道被调度到了102节点，手工通过浏览器打开```http://192.168.0.102:32050```检查服务是否能够正常访问。

## 3. 基于YAML文件创建部署
首先编辑Yaml文件
```yaml
apiVersion: v1
kind: Pod
metadata:                           #元数据信息
  name: tomcat-c                    #kubectl get  pods 和 登陆容器显示的名字
  labels:                           #标签，可以作为查询条件 kubectl get pods -l
    app=tomcat
    node=devops-103
spec:　　　　　　　　　　　　　　　 #规格
  containers:                       #容器
  - name: tomcat                    #容器名称
    image: docker.io/tomcat         #使用的镜像
    ports:
      - containerPort: 8080
    env:　　　　　　　　　　　　　　#设置env，登陆到容器中查看环境变量， DEME_GREETING 的值是 "hello from the enviroment"
    - name:GREETING
      value: "hello from the environment"
```
然后创建Pod。
```bash
[root@devops-101 ~]# kubectl create -f tomcat.yaml 
pod/tomcat-ccb created
[root@devops-101 ~]# kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
tomcat-858b8c476d-vnm98   1/1       Running   2          21h
tomcat-858b8c476d-xl5xl   1/1       Running   3          21h
tomcat-ccb                1/1       Running   0          34s
```

## 4. 扩容部署
在扩容之前，我把devops-102节点也加入到集群中。
```bash
[root@devops-101 ~]# kubectl get nodes
NAME         STATUS    ROLES     AGE       VERSION
devops-101   Ready     master    9h        v1.11.1
devops-102   Ready     <none>    8h        v1.11.1
devops-103   Ready     <none>    7h        v1.11.1
```
然后再执行扩容动作。
```bash
[root@devops-101 ~]# kubectl scale deployments/tomcat --replicas=3
deployment.extensions/tomcat scaled
[root@devops-101 ~]# kubectl get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
tomcat    3         3         3            2           1h
[root@devops-101 ~]# kubectl get pods
NAME                      READY     STATUS              RESTARTS   AGE
tomcat-858b8c476d-cfrtt   0/1       ContainerCreating   0          2m
tomcat-858b8c476d-vnm98   1/1       Running             0          1h
tomcat-858b8c476d-xl5xl   1/1       Running             0          1h
[root@devops-101 ~]# kubectl describe pod tomcat-858b8c476d-cfrtt
Name:           tomcat-858b8c476d-cfrtt
Namespace:      default
Node:           devops-103/192.168.0.103
Start Time:     Tue, 24 Jul 2018 18:29:51 +0800
Labels:         app=tomcat
                pod-template-hash=4146470328
Annotations:    <none>
Status:         Pending
IP:             
Controlled By:  ReplicaSet/tomcat-858b8c476d
Containers:
  tomcat:
    Container ID:   
    Image:          docker.io/tomcat
    Image ID:       
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Waiting
      Reason:       ContainerCreating
    Ready:          False
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-trvqv (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             False 
  ContainersReady   False 
  PodScheduled      True 
Volumes:
  default-token-trvqv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-trvqv
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:
  Type    Reason     Age        From                 Message
  ----    ------     ----       ----                 -------
  Normal  Scheduled  2m         default-scheduler    Successfully assigned default/tomcat-858b8c476d-cfrtt to devops-103
  Normal  Pulling    <invalid>  kubelet, devops-103  pulling image "docker.io/tomcat"
```

## 5. 缩容
```bash
[root@devops-101 ~]# kubectl scale deployments/tomcat --replicas=2
deployment.extensions/tomcat scaled
[root@devops-101 ~]# kubectl get pods
NAME                      READY     STATUS    RESTARTS   AGE
tomcat-858b8c476d-vnm98   1/1       Running   0          1h
tomcat-858b8c476d-xl5xl   1/1       Running   0          1h
```

## 6. 标签功能
创建部署的时候，kubectl会自动帮我们打一个标签，这里是```app=tomcat```。
```bash
[root@devops-101 ~]# kubectl describe deployment
Name:                   tomcat
Namespace:              default
CreationTimestamp:      Tue, 24 Jul 2018 16:35:08 +0800
Labels:                 app=tomcat
Annotations:            deployment.kubernetes.io/revision=1
Selector:               app=tomcat
Replicas:               2 desired | 2 updated | 2 total | 2 available | 0 unavailable
StrategyType:           RollingUpdate
MinReadySeconds:        0
RollingUpdateStrategy:  25% max unavailable, 25% max surge
Pod Template:
  Labels:  app=tomcat
  Containers:
   tomcat:
    Image:        docker.io/tomcat
    Port:         8080/TCP
    Host Port:    0/TCP
    Environment:  <none>
    Mounts:       <none>
  Volumes:        <none>
Conditions:
  Type           Status  Reason
  ----           ------  ------
  Progressing    True    NewReplicaSetAvailable
  Available      True    MinimumReplicasAvailable
OldReplicaSets:  <none>
NewReplicaSet:   tomcat-858b8c476d (2/2 replicas created)
Events:
  Type    Reason             Age   From                   Message
  ----    ------             ----  ----                   -------
  Normal  ScalingReplicaSet  18m   deployment-controller  Scaled up replica set tomcat-858b8c476d to 3
  Normal  ScalingReplicaSet  4m    deployment-controller  Scaled down replica set tomcat-858b8c476d to 2
```
可以使用标签来查询资源，包括Pods和Services。
```bash
[root@devops-101 ~]# kubectl get pods -l app=tomcat
NAME                      READY     STATUS    RESTARTS   AGE
tomcat-858b8c476d-vnm98   1/1       Running   0          1h
tomcat-858b8c476d-xl5xl   1/1       Running   0          1h
[root@devops-101 ~]# kubectl get services -l app=tomcat
NAME      TYPE       CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
tomcat    NodePort   10.98.152.28   <none>        8080:32050/TCP   1h
```
可以给Pods打上自定义的标签。
```bash
[root@devops-101 ~]# kubectl label --overwrite  pod tomcat-858b8c476d-vnm98 node=devops-102
# 这里用了--overwrite属性是因为之前标错了
[root@devops-101 ~]# kubectl describe pods tomcat-858b8c476d-vnm98
Name:           tomcat-858b8c476d-vnm98
Namespace:      default
Node:           devops-102/192.168.0.102
Start Time:     Tue, 24 Jul 2018 16:35:08 +0800
Labels:         app=tomcat
                node=devops-102
                pod-template-hash=4146470328
Annotations:    <none>
Status:         Running
IP:             10.244.2.6
Controlled By:  ReplicaSet/tomcat-858b8c476d
Containers:
  tomcat:
    Container ID:   docker://9f3aa2d3d6c1937d4209a44820c1cd06f7eaf8796848c759e19410358aea4866
    Image:          docker.io/tomcat
    Image ID:       docker-pullable://docker.io/tomcat@sha256:87ad70ceaafd5c71301b081b37ca2795bd6c7c1a5599a8c92c9447bbd225ae47
    Port:           8080/TCP
    Host Port:      0/TCP
    State:          Running
      Started:      Tue, 24 Jul 2018 16:35:37 +0800
    Ready:          True
    Restart Count:  0
    Environment:    <none>
    Mounts:
      /var/run/secrets/kubernetes.io/serviceaccount from default-token-trvqv (ro)
Conditions:
  Type              Status
  Initialized       True 
  Ready             True 
  ContainersReady   True 
  PodScheduled      True 
Volumes:
  default-token-trvqv:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  default-token-trvqv
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>
```

## 7. 删除部署
```bash
$ kubectl delete pod-name
```


## 参考资料

1. [Using kubectl to Create a Deployment](https://kubernetes.io/docs/tutorials/kubernetes-basics/deploy-app/deploy-intro/)
2. [kubernetes 安装学习](https://www.cnblogs.com/fengjian2016/p/6392900.html)
3. [kubernetes step by step](https://www.jianshu.com/p/0e8064e16451)












