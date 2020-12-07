---
title: Docker Composer 使用介绍
date: 2020-04-15 19:23:30
tags: 
keywords: docker, docker-compose, docker-compose.yml
description: Docker-Compose 项目是 [Docker](https://www.docker.com) 官方的开源项目，负责实现对 Docker 容器集群的快速编排。Compose允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。
---

Docker-Compose 项目是 [Docker](https://www.docker.com) 官方的开源项目，负责实现对 Docker 容器集群的快速编排。Compose允许用户通过一个单独的 docker-compose.yml 模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

> 本文主要介绍了 Docker-compose 的使用方法，如果读者对于 docker 不是很熟悉，可以看看我这里的系列介绍文章 [Docker系列文章](http://edulinks.cn/2020/10/16/20201016-kubernetes-articles/index.html#Docker-系列文章)。

<!-- more -->

[TOC]

## 简介

Docker-Compose项目是Docker官方的开源项目，负责实现对Docker容器集群的快速编排。Compose允许用户通过一个单独的docker-compose.yml模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。Docker-Compose项目由Python编写，调用Docker服务提供的API来对容器进行管理。因此，只要所操作的平台支持Docker API，就可以在其上利用Compose来进行编排管理。

Docker-Compose将所管理的容器分为三层，分别是`工程（project）`，`服务（service）`以及`容器（container）`。Docker-Compose运行目录下的所有文件（docker-compose.yml，extends文件或环境变量文件等）组成一个工程，若无特殊指定工程名即为当前目录名。一个工程当中可包含多个服务，每个服务中定义了容器运行的镜像，参数，依赖。一个服务当中可包括多个容器实例，Docker-Compose并没有解决负载均衡的问题，**因此需要借助其它工具实现服务发现及负载均衡**。

Docker-Compose的工程配置文件默认为docker-compose.yml，可通过环境变量COMPOSE_FILE或-f参数自定义配置文件，其定义了多个有依赖关系的服务及每个服务运行的容器。
使用一个Dockerfile模板文件，可以让用户很方便的定义一个单独的应用容器。在工作中，经常会碰到需要多个容器相互配合来完成某项任务的情况。例如要实现一个Web项目，除了Web服务容器本身，往往还需要再加上后端的数据库服务容器，甚至还包括负载均衡容器等。

## 常用命令

### docker-compose
常见的命令格式如下
```sh
$ docker-compose [-f <arg>...] [options] [COMMAND] [ARGS...]
```
常见的选项包括
* -f，–file FILE指定Compose模板文件，默认为docker-compose.yml，可以多次指定。
* -p，–project-name NAME指定项目名称，默认将使用所在目录名称作为项目名。
* -x-network-driver 使用Docker的可拔插网络后端特性（需要Docker 1.9+版本）
* -x-network-driver DRIVER指定网络后端的驱动，默认为bridge（需要Docker 1.9+版本）
* -verbose输出更多调试信息
* -v，–version打印版本并退出


### docker-compose up
```sh
$ docker-compose up [options] [--scale SERVICE=NUM...] [SERVICE...]
```
选项包括：
* -d 在后台运行服务容器
* –no-color 不使用颜色来区分不同的服务的控制输出
* –no-deps 不启动服务所链接的容器
* –force-recreate 强制重新创建容器，不能与–no-recreate同时使用
* –no-recreate 如果容器已经存在，则不重新创建，不能与–force-recreate同时使用
* –no-build 不自动构建缺失的服务镜像
* –build 在启动容器前构建服务镜像
* –abort-on-container-exit 停止所有容器，如果任何一个容器被停止，不能与-d同时使用
* -t, –timeout TIMEOUT 停止容器时候的超时（默认为10秒）
* –remove-orphans 删除服务中没有在compose文件中定义的容器
* –scale SERVICE=NUM 设置服务运行容器的个数，将覆盖在compose中通过scale指定的参数
```sh
$ docker-compose up
```
启动所有服务
```sh
$ docker-compose up -d
```
在后台所有启动服务
-f 指定使用的Compose模板文件，默认为docker-compose.yml，可以多次指定。
```sh
$ docker-compose -f docker-compose.yml up -d
```

### docker-compose ps
列出项目中目前所有的容器

### docker-compose start
启动已经存在的服务容器。
```sh
$ docker-compose start [SERVICE...]
$ docker-compose start
```

### docker-compose stop
停止正在运行的容器，可以通过docker-compose start 再次启动。
```sh
$ docker-compose stop [options] [SERVICE...]
```

### docker-compose restart
重启项目中的服务。
```sh
$ docker-compose restart [options] [SERVICE...]
```
选项包括：
* -t, –timeout TIMEOUT，指定重启前停止容器的超时（默认为10秒）

### docker-compose down
停止和删除容器、网络、卷、镜像。
```sh
$ docker-compose down [options]
```
选项包括
* –rmi type，删除镜像，类型必须是：all，删除compose文件中定义的所有镜像；local，删除镜像名为空的镜像
* -v, –volumes，删除已经在compose文件中定义的和匿名的附在容器上的数据卷
* –remove-orphans，删除服务中没有在compose中定义的容器

### docker-compose logs
查看服务容器的输出。默认情况下，docker-compose将对不同的服务输出使用不同的颜色来区分。可以通过–no-color来关闭颜色。
```sh
$ docker-compose logs [options] [SERVICE...]
```

### docker-compose build
构建（重新构建）项目中的服务容器。
```sh
$ docker-compose build [options] [--build-arg key=val...] [SERVICE...]
```
选项包括：
* –compress 通过gzip压缩构建上下环境
* –force-rm 删除构建过程中的临时容器
* –no-cache 构建镜像过程中不使用缓存
* –pull 始终尝试通过拉取操作来获取更新版本的镜像
* -m, –memory MEM为构建的容器设置内存大小
* –build-arg key=val为服务设置build-time变量


### docker-compose pull
拉取服务依赖的镜像。
```sh
$ docker-compose pull [options] [SERVICE...]
```
选项包括：
* –ignore-pull-failures，忽略拉取镜像过程中的错误
* –parallel，多个镜像同时拉取
* –quiet，拉取镜像过程中不打印进度信息

### docker-compose rm
删除所有（停止状态的）服务容器。
```sh
$ docker-compose rm [options] [SERVICE...]
```
选项包括：
–f, –force，强制直接删除，包括非停止状态的容器
-v，删除容器所挂载的数据卷

### docker-compose run
在指定服务上执行一个命令。
```sh
$ docker-compose run [options] [-v VOLUME...] [-p PORT...] [-e KEY=VAL...] SERVICE [COMMAND] [ARGS...]
```

## docker-compose 模版文件
Compose允许用户通过一个docker-compose.yml模板文件（YAML 格式）来定义一组相关联的应用容器为一个项目（project）。

Compose模板文件是一个定义服务、网络和卷的YAML文件。Compose模板文件默认路径是当前目录下的docker-compose.yml，可以使用.yml或.yaml作为文件扩展名。

Docker-Compose标准模板文件应该包含version、services、networks 三大部分，最关键的是services和networks两个部分。

```yaml
version: "2.1"
services:
  img-name:
    image: xxxx
```

> Compose目前有三个版本分别为Version 1，Version 2，Version 3，Compose区分Version 1和Version 2（Compose 1.6.0+，Docker Engine 1.10.0+）。Version 2支持更多的指令。Version 1将来会被弃用。

## 挂载文件

docker-compose支持两种方式设置持久化的文件
```yaml
servicename:
  image: image-name
  volumes:
  - /path/to/file:/path/to/container/file
```
这种方式将文件直接挂载到容器中，使用起来比较直观，但是需要管理本地路径。

```yaml
servicename:
  image: image-name
  volumes:
  - volume-name:/path/to/container/file
volumes:
  volume-name: /path/to/local/file
```

使用`docker volume ls`命令可以查看本地挂载的文件。
使用`docker volume inspect volume-name`命令可以查看具体的真实地址。

## 应用实例

利用docker-compose，可以快速帮助我们将不同的镜像编排组合成自己需要的服务，感兴趣的可以继续阅读我的其他文章。

* [使用Docker快速搭建PHP开发环境](https://www.edulinks.cn/2020/04/17/20200415-qucik-lnmp-dev-environment/)
* [Docker-compose 建立ELK集群](https://www.edulinks.cn/2020/01/15/20200115-docker-compose-elk-cluster/)
* [Flink101-快速示例](https://www.edulinks.cn/2019/11/19/20191119-flink-101-quick-start/)

## 参考资料

1. [Docker快速入门——Docker-Compose](https://www.cnblogs.com/phpk/p/11205467.html)
2. [docker-compose-volumes的说明](https://www.cnblogs.com/lori/archive/2018/10/24/9843190.html)
3. [docker-compose命令及yaml文件](https://www.cnblogs.com/g2thend/p/11746679.html)