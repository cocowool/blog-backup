---
title: 利用 Docker 构建一个简单的 java 开发编译环境
tags:
category: 开发
---

目前 Java 语言的版本很多，除了常用的 Java 8，有一些遗留项目可能使用了 Java 7，也可能有一些比较新的的项目使用了 Java 10 以上的版本。如果想切换自己本地的 Java 开发环境，折腾起来还是需要花费一些时间的，并且日后在不同版本间切换每次都要折腾一次。

Docker 的出现让我们维护不同版本的开发编译环境变得简单，如果你还不知道什么是 Docker 可以看看 [Docker 入门介绍](http://edulinks.cn/2018/06/20/20180620-docker-overview/)。我们可以采用两种方式来构建 java 的开发环境，一种是在容器内编译运行，一种是在容器外编译运行，下面分别来看看具体如何操作。

## 容器内编译与运行



## 容器外编译与运行



## 写在最后

本文仅仅构建了一个简单的 java 开发环境，对于复杂的工程常常使用 maven 或者 graddle 进行构建，这种项目能否使用本文提到的方式来构建呢，敬请期待本系列的下一篇文章，关注公众号实时掌握。



## 参考资料

1. [Docker - Create a Java development environment](https://childofcode.com/docker-create-a-java-development-environment/)

