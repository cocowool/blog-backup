---
title: 将Java程序制作为镜像
date: 2021-04-08 08:46:10
tags:
keywords: docker, java, java docker, java程序镜像
category:
description: 通过简单的命令将Java程序做成镜像，方便快速的进行应用部署。
---

## 具体步骤
首先建立一个docker文件夹，用于存放构建镜像需要的文件，例如jre和jar包，还有镜像文件`Dockerfile`。

将相关的jre和jar包拷贝到docker文件夹中。

编写Dockerfile，例如下面我以自己写过的java-metrics为例演示。
```dockerfile
FROM frolvlad/alpine-glibc
ADD jre-8u231-linux-x64.tar.gz /usr/java/jdk
ENV JAVA_HOME /usr/java/jdk/jre1.8.0_231
ENV PATH ${PATH}:${JAVA_HOME}/bin
COPY metrics-core-4.0.5.jar /home/lib/metrics-core-4.0.5.jar
COPY slf4j-api-1.7.25.jar /home/lib/slf4j-api-1.7.25.jar
COPY slf4j-simple-1.7.28.jar /home/lib/slf4j-simple-1.7.28.jar
COPY learn-metrics-1.0.jar /home/learn-metrics-1.0.jar
CMD java -Xbootclasspath/a:/home/lib/metrics-core-4.0.5.jar:/home/lib/slf4j-api-1.7.25.jar:/home/lib/slf4j-simple-1.7.28.jar -jar /home/learn-metrics-1.0.jar timer
```

然后执行以下命令
```sh
$ docker build -t imagename:version . #生成指定版本的镜像
$ docker run imagename:version
```

顺利的话，就能够看到程序的输出了。

## 参考资料
1. [Docker Java程序镜像制作](https://www.cnblogs.com/freedom-only/p/11294103.html)