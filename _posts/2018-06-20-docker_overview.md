---
title: 告别新手，一篇文章助你入门Docker
date: 2018-06-20 11:12
tag: 
---

Docker 是一个应用程序开发、部署、运行的平台，使用 go 语言开发。相较于传统的主机虚拟化，Docker 提供了轻量级的应用隔离方案，并且为我们提供了应用程序快速扩容、缩容的能力。

### Docker Engine
先来看一下 Docker Engine 的结构，它是一个Client-Server应用程序，包含了三个主要部分：dockerd守护进程、REST API接口层、cli接口层(管理容器、镜像、网络、存储等等)。
![](2018-06-20-docker_overview/39469-20180620111050822-168274068.png)

### Docker 架构
Docker采用的是CS架构，docker client 通过Unix套接字或者网络接口访问 docker daemon，从而完成容器、镜像等内容的管理。
![](2018-06-20-docker_overview/39469-20180620111102824-1462399429.png)

#### Docker Daemon
Docker的守护进程监听对于API的请求，并且管理Docker对象，包括容器、镜像、网络、存储等。不同守护进程之间可以互相通信，从而构成集群服务。

#### Docker Client
Docker 客户端```docker```命令是与Docker交互的主要方式。

#### Docker 仓库
Docker仓库保存Docker镜像，可以通过```docker pull```以及```docker push```来下载、上传镜像文件。

#### Docker 对象
**1. 镜像**
镜像是一个用来构建容器的只读模版，通常一个镜像会依赖其他的镜像。例如我们编写的一个Node程序需要依赖Node环境，那在构建这个应用镜像时就需要依赖基础的Node镜像。
我们可以创建自己的镜像，也可以使用仓库中已经创建好的镜像。创建镜像需要创建一个```Dockerfile```文件。每个Dockerfile定义镜像文件中的一层，当定义发生变化的时候，只需要更新着一层的文件即可。
**2. 容器**
容器是一个运行时状态下的镜像，通过docker命令我们可以创建、启动、停止、删除容器。例如以下命令：
```bash
$ docker run -i -t ubuntu /bin/bash
```
实际上经过了6个步骤：1、如果本地没有**ubuntu**镜像，那么首先执行```docker pull ubuntu```镜像文件到本地；2、自动创建一个容器，相当于命令```dock container create```；3、Docker分配一块文件系统给容器；4、Docker创建网络接口、分配网络地址；5、启动容器，并且执行```/bin/bash```命令；6、我们可以在bash中执行命令，执行exit后，退出ubuntu的命令行，同时容器关闭。
**3. 网络**
Docker的网络子系统是可插拔的，支持bridge、host、overlay、macvlan、none等网络模式。熟悉虚拟机的同学可能对这些有概念，想要了解更多参考[这里](https://docs.docker.com/network/network-tutorial-standalone/)。
**4. 存储**
默认情况下，容器中的应用生成的所有文件都存放在一个可写的容器层，意味着这些数据的生命周期和容器保持一致，一旦容器重启数据就丢了。另外，这些文件与容器高度关联，想要将这些文件分享给其他的容器或者设备非常困难。
为此，Docker提供了两种方案解决数据问题：**Volumes**、**bind mount**。不管使用哪种方式，容器内看起来都是一样的，或者作为一个文件夹存在、或者作为一个文件存在。
上图说明了不同方式的区别，**Volumes**是存在本地文件系统中的一部分，其他应用程序不能对这个文件系统进行修改，Linux下在```/var/lib/docker/volumes```。这是数据持久化的最好方案。**Bind Mount**允许将主机中任何位置的数据挂载，这些数据的读写没有收到保护。**tmps**是存储在主机内存中的数据。

### 技术依赖
Docker 严重依赖操作系统的虚拟化特性，主要包括以下几点。

#### namespaces

#### Control Groups
Control Groups 也称```cgroups```，主要是解决Linux系统中进程间的资源隔离与权限访问的相关问题。

#### Union file Systems

#### container format
容器格式是结合了namespace、cgroups、UnionFS在内的一个封装。

## 参考资料：
1. [Docker Overview](https://docs.docker.com/engine/docker-overview/)
2. [Linux Namespaces机制——实现](https://www.cnblogs.com/lisperl/archive/2012/05/03/2480573.html)
3. [Linux namespaces](https://en.wikipedia.org/wiki/Linux_namespaces)
4. [cgroups](https://en.wikipedia.org/wiki/Cgroups)












