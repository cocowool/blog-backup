---
title: 谈一下Docker与Kubernetes集群的日志和日志管理
date: 2018-08-30 10:49
tag: Kubernetes
keywords: kubernetes, k8s, 日志, 日志管理
description: 日志对于我们管理Kubernetes集群及其上的应用具有非常重要的作用，特别是在出现故障或者Bug的时候。本文介绍了几种kubernetes集群日志管理的方法。
---

> 本文的测试环境为CentOS 7.3，Kubernetes集群为1.11.2，安装步骤参见[kubeadm安装kubernetes V1.11.1 集群](https://www.edulinks.cn/2018/07/24/20180724-kubeadm-install-kubernetes/) 。想要了解更多 Kubernetes 相关知识，可以阅读 [Kubernetes 系列学习文章](http://edulinks.cn/2020/10/16/20201016-kubernetes-articles/)。

日志对于我们管理Kubernetes集群及其上的应用具有非常重要的作用，特别是在出现故障或者Bug的时候。如果你能回答下面几个问题，那么可以不用再看本文了，如果不能回答，本文可能正好适合你。

* Docker都会产生哪些日志？
* Docker产生的日志都放在哪里？
* Docker的日志的分割、清理策略默认为什么？
* 如何配置Docker日志的分割、清理策略？
* Kubernetes都会产生哪些日志？
* Kubernetes产生的日志都存放在那里？
* 如何集中管理Kubernetes产生的日志？

## 1. Docker的日志处理方案
Docker产生的日志有两类:

* 一类是Docker引擎日志。Docker引擎日志在不同的操作系统下管理方式不一样，在Centos中是通过journalctl来进行管理。
* 一类是容器日志。容器中的应用产生的日志默认都输出到```stdout```和```stderr```中，可以通过```docker logs```来访问。Docker为容器日志提供了多种实现机制称为logging driver。通过```docker info```可以查看本机使用的logging driver，默认为```json-file```形式，这种形式下每个容器的日志默认以json格式存储在```/var/lib/docker/containers/<容器id>/<容器id>-json.log```下。下面两种情况使用```docker logs```看不到什么有用的信息：
  * 容器内的应用不是交互式应用，而是实现了自己的日志输出，例如对于Apache、Nginx等Web服务，通常会将访问日志和错误日志记录到不同的文件，而不是打到标准输出和错误输出。
  * 使用不同的[logging driver](https://docs.docker.com/config/containers/logging/configure/)将日志送到了文件、外部服务器、数据库等集中的日志后台。

Docker目前支持的logging driver类型：

* none
* json-file
* syslog
* journald
* gelf
* fluentd
* awslogs
* splunk
* etwlogs
* gcplogs
* logentries

### 1.1 配置logging driver
运行容器时，可以通过命令行参数指定logging driver的类型。
```bash
bogon:log rousseau$ docker run -it --log-driver none alpine sh
/ # ls
#切换一个终端
bogon:log rousseau$ docker ps
CONTAINER ID        IMAGE               COMMAND             CREATED             STATUS              PORTS               NAMES
8ec61e8feb61        alpine              "sh"                13 seconds ago      Up 12 seconds                           xenodochial_ptolemy
bogon:log rousseau$ docker logs 8ec61e8feb61
Error response from daemon: configured logging driver does not support reading
bogon:log rousseau$ docker inspect -f '{{.HostConfig.LogConfig.Type}}' 8ec61e8feb61
none
```
> 对于日志输出到文件的应用，可以通过软连接的方式将日志输出到标准输出和标准错误输出。例如：The official nginx image creates a symbolic link from /var/log/nginx/access.log to /dev/stdout, and creates another symbolic link from /var/log/nginx/error.log to /dev/stderr, overwriting the log files and causing logs to be sent to the relevant special device instead.

> 对于多行格式的日志信息，有一点需要特别关注。The Docker json logging driver treats each line as a separate message. When using the Docker logging driver, there is no direct support for multi-line messages. You need to handle multi-line messages at the logging agent level or higher.


### 1.3 日志分割策略配置
在CentOS中，可以通过修改```/etc/docker/daemon.json```来配置Docker的日志分割策略。
```json
{
    "log-driver": "json-file",
    "log-opts": {
        "max-size": "9m", # Max size of the log files.
        "max-file": "5" # The maximum number of log files that can be present.
    }
}
```
> 总结：对于单机的Docker来说，可以将日志以json-file的形式存储到本地磁盘，同时提供了根据文件大小进行文件分割的配置，也提供了根据文件数量进行文件清理的策略。配置好这两个参数，基本上就不需要担心容器应用将磁盘写满的风险了。而Docker引擎本身的日志则是通过journald来进行管理的。


## 2. Kubernetes的日志处理方案
Kubernetes的日志管理方式与Docker有所不同，因为容器封装在Pod中，当遇到Pod被删除或者Node节点故障的情况下，日志会被删除，单纯依靠Docker本身的日志机制将无法在故障后查询日志，因此在管理集群时需要认真考虑日志的管理问题。
Kubernetes集群的日志包括以下几种：

* 各种event事件，可以通过```kubectl describe pod```查看到详细信息
* 容器应用产生的日志
* Node节点上Docker Daemon产生的日志
* Node节点上kubelet产生的日志

### 2.1 日志的位置
Kubernetes集群中包含众多组件，在用kubeadm方式安装的集群上，有些组件不是容器化运行的，包括Master节点的kubelet、kube-scheduler、kube-controller-manager、kube-apiserver，Node节点上运行了kubelet和kube-proxy。有些组件是容器化方式运行的，当然包括我们的应用。因此日志就分为两类：容器化组件及应用的日志，非容器化组件的日志。

#### 2.1.1 容器化组件及应用的日志管理
对于容器化的组件和应用，其日志的管理方式都通过Docker的log-driver来进行指定，因此Kubernetes本身并不提供日志分割的功能。有两种方式来实现日志的分割：

* 通过Docker配置```log-opt```参数，与上一节的方式相同。
* 通过其他的脚本，例如[kube-up.sh](https://github.com/kubernetes/kubernetes/blob/master/cluster/gce/gci/configure-helper.sh)来分割日志文件。
![](20180830-docker-kubernetes-log-location/logging-node-level.png)
> 如果使用外部工具对日志进行了分割，再通过```docker logs```目录查看日志时，仅返回最新的文件中的内容。


#### 2.1.2 非容器化的组件日志管理。
系统组件也分为两种，一种是运行在容器中的应用，包括```scheduler```、```kube-proxy```等；一种是没有运行在容器中的应用，包括```kubelet```和容器运行环境。这些组件的日志都是通过```journald```来进行管理的。

### 2.2 集群级别的日志管理
Kubernetes本身没有提供集群级别的日志管理功能，如想实现集群级别的日志管理有三种方案：

* 在每个Node中运行日志采集代理，将日志收集到集中的日志管理平台。这种方案对应用没有侵入性，是优选方案。
* 在前一种方案的基础上，在每个应用Pod中增加Sidecar容器来实现日志的分离。
* 应用直接将日志输出到统一的日志管理平台，不在本地落地，这种方案对于应用的侵入性较大
* 

#### 2.3.1 日志采集代理方案
![](20180830-docker-kubernetes-log-location/logging-with-node-agent.png)
这种方案在每台Node上运行一个专用的日志采集代理，可以是容器化的应用（需要能够在容器内访问节点上的日志文件），可以采用```DaemonSet```来确保代理在每个Node中都运行。

#### 2.3.2 Sidecar方案
Sidecar的本意是挎斗摩托车，这种车型在摩托车旁边加了一个边斗来提高运载量，是二战时期日军广泛使用的一种运输工具。
![](20180830-docker-kubernetes-log-location/36ced2e10a12598fc7daab9820de4396.jpg)
类似于挎斗摩托车，Sidecar方案是在应用Pod中增加一个或多个负责处理日志的容器，根据增加容器的作用，可以分为日志分离和容器内代理。

##### 2.3.2.1 日志分离
![](20180830-docker-kubernetes-log-location/logging-with-streaming-sidecar.png)
> 尽管CPU和内存资源的消耗非常少，但是读取日志文件并且重写到标准输出会加倍磁盘的负载，这一点需要特别注意。


##### 2.3.2.2 容器内代理
![](20180830-docker-kubernetes-log-location/logging-with-sidecar-agent.png)
采用这种方案，通过容器内代理直接将应用的日志上传到统一的日志管理平台，会增加Pod的资源消耗，同时无法通过```kubectl logs```命令来查看日志。

#### 2.3.3 流式处理方案

这种方案需要对应用做改造，应用通过流式日志的接口，直接将日志发送到在线的日志集群中。好处是无论Pod发生重启、迁移等不可预知的操作，只要服务能够起来，都能够将日志发送并保存下来。

![](20180830-docker-kubernetes-log-location/logging-from-application.png)


## 参考资料

1. [Logging Architecture](https://kubernetes.io/docs/concepts/cluster-administration/logging/)
2. [Docker日志都在哪里？怎么收集？](https://www.cnblogs.com/YatHo/p/7866029.html)
3. [Systemd之journalctl](https://www.cnblogs.com/itxdm/p/Systemd_log_system_journalctl.html)
4. [Kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)
5. [Docker Logging Overview](https://docs.docker.com/engine/admin/logging/overview)
6. [fluentd](http://www.fluentd.org/)
7. [Stackdriver](https://kubernetes.io/docs/tasks/debug-application-cluster/logging-stackdriver/)
8. [TLDC](http://www.tldp.org/LDP/abs/html/index.html)
9. [Kubernetes日志收集](https://www.cnblogs.com/keithtt/p/6410249.html)
10. [Kubernetes容器集群中的日志系统集成实践](http://www.yunweipai.com/archives/8797.html)
11. [Where are the Kubernetes kublet logs located?](https://stackoverflow.com/questions/34113476/where-are-the-kubernetes-kubelet-logs-located)
12. [Kubernetes Log Analysis with Fluentd, Elasticsearch and Kibana](https://logz.io/blog/kubernetes-log-analysis/)
13. [Kubernetes Log File Locations](https://wiki.mikejung.biz/Kubernetes#Kubernetes_Commands_and_Log_File_Locations)
14. [Kubernetes Logs](https://github.com/kubernetes/kubernetes/issues/26726)












