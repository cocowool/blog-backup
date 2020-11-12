---
title: 利用 Docker 构建一个简单的 java 开发编译环境
tags:
category: 开发
---

目前 Java 语言的版本很多，除了常用的 Java 8，有一些遗留项目可能使用了 Java 7，也可能有一些比较新的的项目使用了 Java 10 以上的版本。如果想切换自己本地的 Java 开发环境，折腾起来还是需要花费一些时间的，并且日后在不同版本间切换每次都要折腾一次。

Docker 的出现让我们维护不同版本的开发编译环境变得简单，如果你还不知道什么是 Docker 可以看看 [Docker 入门介绍](http://edulinks.cn/2018/06/20/20180620-docker-overview/)。我们可以采用两种方式来构建 java 的开发环境，一种是在容器内编译运行，一种是在容器外编译运行，下面分别来看看具体如何操作。

>  准备工作：首先确保已经安装了 Docker ，如果方便可以提前把 `openjdk:8` 的镜像拉取下来，到时可以节省大家的时间。本文在 macOs 10.15.7 、Docker 19.03.8 的环境下验证通过。

## 容器内编译与运行

本文以一个简单的 Helloworld 程序为例，工程的文件目录结构及代码如下。

```shell
$ ls -lh
total 24
-rw-r--r--  1 shiqiang  staff    60B 11 11 19:02 Dockerfile
-rw-r--r--  1 shiqiang  staff   123B 11 11 19:02 Helloworld.java
```

代码的内容。

```java
public class Helloworld {
    public static void main(String args[]){
        System.out.println("Hello world.\n");
    }
}
```

做好以上准备后，就可以编辑 Dockerfile 的内容。

```dockerfile
FROM openjdk:8  							#基于 openjdk:8 构建镜像
COPY . /usr/src/myapp  			  #将目录内的代码拷贝到镜像中 /usr/src/myapp 的位置
WORKDIR /usr/src/myapp  			#将 /usr/src/myapp 设为工作目录
RUN javac Helloworld.java  	  #运行编译命令，编译 Helloworld.java 程序
CMD ["java", "Helloworld"]    #执行编译出来的 Helloworld
```

构建镜像文件。

```shell
$ docker build -t java_in_docker_env_8 . 
$ docker images # 使用 docker images 命令可以看到构建出来的 java_in_docker_env_8 镜像
```

运行容器。

```shell
$ docker run -it --rm java_in_docker_env_8
Hello world.

$ ls -lh                                  
total 24
-rw-r--r--  1 shiqiang  staff   112B 11 12 10:53 Dockerfile
-rw-r--r--  1 shiqiang  staff   123B 11 12 10:49 Helloworld.java
```

可以看到程序的输出，并且看到当前目录下并有没编译出来的结果。但是这种方式每次启动容器的时候都要进行编译，并且没有办法把编译结果进行分享，下一节的内容介绍如何在容器外保存编译结果。

## 容器外编译与运行

工作目录和代码准备与上一节相同，Dockerfile 文件与上一节略有不同。

```dockerfile
FROM openjdk:8
COPY . /usr/src/myapp
WORKDIR /usr/src/myapp
```

构建镜像文件。

```shell
$ docker build -t java_env_8
$ docker images # 使用 docker images 命令可以看到构建出来的 java_env_8 镜像
```

编译文件。

```shell
$ docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp java_env_8 javac Helloworld.java
$ ls -lh	# 可以看到编译出来的结果
total 32
-rw-r--r--  1 shiqiang  staff   112B 11 12 10:53 Dockerfile
-rw-r--r--  1 shiqiang  staff   427B 11 12 11:09 Helloworld.class
-rw-r--r--  1 shiqiang  staff   123B 11 12 10:49 Helloworld.java
```

运行程序。

```shell
$ docker run --rm -v "$PWD":/usr/src/myapp -w /usr/src/myapp java_env_8 java Helloworld      
Hello world.


```

## 写在最后

本文仅仅构建了一个简单的 java 开发环境，对于复杂的工程常常使用 maven 或者 graddle 进行构建，这种项目能否使用本文提到的方式来构建呢，敬请期待本系列的下一篇文章，也欢迎大家关注公众号获得最新的文章推送。如果想要进一步了解 Docker 的相关知识，可以参考我之前的文章。

* [Docker 入门介绍](http://edulinks.cn/2018/06/20/20180620-docker-overview/)
* [Docker Compose 使用介绍](http://edulinks.cn/2020/04/15/20200415-docker-compose/)
* [在Redhat 7.3中采用离线方式安装Docker](http://edulinks.cn/2018/07/11/20180711-install-docker-ce-in-redhat-73/)
* [创建自己的Docker基础镜像](http://edulinks.cn/2018/06/27/20180627-make-your-own-base-docker-image/)
* [Docker存出载入镜像](http://edulinks.cn/2018/07/16/20180716-docker-save-load-image/)
* [使用 Docker 快速搭建PHP开发环境](http://edulinks.cn/2020/04/17/20200415-qucik-lnmp-dev-environment/)
* [Docker Compose 建立ELK集群](http://edulinks.cn/2020/01/15/20200115-docker-compose-elk-cluster/)

## 参考资料

1. [Docker - Create a Java development environment](https://childofcode.com/docker-create-a-java-development-environment/)

