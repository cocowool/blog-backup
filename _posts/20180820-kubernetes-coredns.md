---
title: CoreDNS介绍
date: 2018-08-20 19:07
tag: Kubernetes
keywords: kubernetes, k8s, coredns
description: CoreDNS在Kubernetes1.11版本已经做为GA功能释放，成为Kubernetes默认的DNS服务替代了Ku be-DNS，目前是kubeadm、kube-up、minikube和kops安装工具的默认选项。
---

> 本文介绍 CoreDNS 相关配置以及验证方法，实验环境为 Kubernetes 1.11，搭建方法参考[kubeadm安装kubernetes V1.11.1 集群](https://www.edulinks.cn/2018/07/24/20180724-kubeadm-install-kubernetes/) 。想要了解更多 Kubernetes 相关知识，可以阅读 [Kubernetes 系列学习文章](http://edulinks.cn/2020/10/16/20201016-kubernetes-articles/)。


## busybox 的槽点
开始之前先吐槽一下busybox中的```nslookup```命令。这个命令应该是实现的不是很完全，导致我在测试DNS的成功，得到了错误的信息。先来看一下
```bash
[root@devops-101 ~]# kubectl run busybox1 --rm -it --image=docker.io/busybox /bin/sh
If you don't see a command prompt, try pressing enter.
/ # nslookup kubernetes.default
Server:		172.17.0.10
Address:	172.17.0.10:53

** server can't find kubernetes.default: NXDOMAIN

*** Can't find kubernetes.default: No answer
```
看起来像是DNS没有响应，慢着，使用带nslookup的alphine试一下。
```bash
[root@devops-101 ~]# kubectl run dig --rm -it --image=docker.io/azukiapp/dig /bin/sh
If you don't see a command prompt, try pressing enter.
/ # dig @172.17.0.10 kubernetes.default.svc.cluster.local +noall +answer

; <<>> DiG 9.10.3-P3 <<>> @172.17.0.10 kubernetes.default.svc.cluster.local +noall +answer
; (1 server found)
;; global options: +cmd
kubernetes.default.svc.cluster.local. 5	IN A	172.17.0.1
/ # nslookup kubernetes.default
Server:		172.17.0.10
Address:	172.17.0.10#53

Name:	kubernetes.default.svc.cluster.local
Address: 172.17.0.1

/ # nslookup www.baidu.com
Server:		172.17.0.10
Address:	172.17.0.10#53

Non-authoritative answer:
www.baidu.com	canonical name = www.a.shifen.com.
Name:	www.a.shifen.com
Address: 220.181.112.244
Name:	www.a.shifen.com
Address: 220.181.111.188

/ # nslookup kubernetes.default
Server:		172.17.0.10
Address:	172.17.0.10#53

Name:	kubernetes.default.svc.cluster.local
Address: 172.17.0.1
```
好好的啊！就是这个原因，busybox坑了我好几天。

## CoreDNS
CoreDNS在Kubernetes1.11版本已经做为GA功能释放，成为Kubernetes默认的DNS服务替代了Ku be-DNS，目前是kubeadm、kube-up、minikube和kops安装工具的默认选项。
> ```Stubdomain```and```upstreamnameserver```in kube-dns translates to the proxy in CoreDNS. The```federation```in kube-dns has an equivalent```federation```in CoreDNS.


## 配置文件
使用kubeadm安装CoreDNS，会使用ConfigMap做为配置文件。这份配置文件，会默认使用宿主机的DNS服务器地址。
```bash
[root@devops-101 ~]# kubectl -n kube-system get configmap coredns -oyaml
apiVersion: v1
data:
  Corefile: |
    .:53 {
        errors
        health
        kubernetes cluster.local in-addr.arpa ip6.arpa {
           pods insecure
           upstream
           fallthrough in-addr.arpa ip6.arpa
        }
        prometheus :9153
        proxy . /etc/resolv.conf
        cache 30
        reload
    }
kind: ConfigMap
metadata:
  creationTimestamp: 2018-08-20T07:01:55Z
  name: coredns
  namespace: kube-system
  resourceVersion: "193"
  selfLink: /api/v1/namespaces/kube-system/configmaps/coredns
  uid: ec72baa4-a446-11e8-ac92-080027b7c4e9
```
配置文件各项目的含义


## 检查COreDNS运行状况
检查Pod状态
```bash
[root@devops-101 ~]# kubectl -n kube-system get pods -o wide
NAME                                 READY     STATUS    RESTARTS   AGE       IP              NODE
coredns-78fcdf6894-52gp9             1/1       Running   4          4h        172.16.0.11     devops-101
coredns-78fcdf6894-mkvqn             1/1       Running   4          4h        172.16.0.10     devops-101
etcd-devops-101                      1/1       Running   4          3h        192.168.0.101   devops-101
```
检查部署
```bash
[root@devops-101 ~]# kubectl -n kube-system get deployments
NAME      DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
coredns   2         2         2            2           4h
```
验证可以采用本文刚开始部分提到的方法。

## 参考资料

1. [nslookup not working with flannel as network: nameserver 10.96.0.10](https://github.com/kubernetes/kubernetes/issues/44833)
2. [使用 kubeadm 搭建 kubernetes1.10 集群](https://www.cnblogs.com/cp-miao/p/8891200.html)
3. [CoreDNS for Kubernetes Service Discovery, Take 2](https://coredns.io/2017/03/01/coredns-for-kubernetes-service-discovery-take-2/)
4. [Migration from kube-dns to CoreDNS](https://coredns.io/2018/05/21/migration-from-kube-dns-to-coredns/)
5. [Deploying Kubernetes with CoreDNS using kubeadm](https://coredns.io/2018/01/29/deploying-kubernetes-with-coredns-using-kubeadm/)
6. [dns service discovery fails on 1.11 with coredns (new default)](https://github.com/kubernetes/kubernetes/issues/66629)












