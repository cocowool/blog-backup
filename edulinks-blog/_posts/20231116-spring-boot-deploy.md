---
title: Springboot 工程的部署方式
date: 2023-11-16 08:32:30
keywords: springboot, 部署
description: 本文介绍 Springboot 工程的几种部署方式，包括 Tomcat 部署和容器部署。
---

## War 包部署到外部 Tomcat

Tomcat 是一个免费开源的 Java Web 应用服务器，实现了 Java Servlet 和 JavaServer Pages 规范，属于轻量级应用服务器，没有商业中间件如 Weblogic、Websphere 等集群管理能力。在中小型系统被广泛使用，是Java Web开发调试的首选。

本文选择使用 Docker 运行 Tomcat，使用的 Tomcat 版本为 9.0.31 。
```
$ docker 
```

## Docker 方式部署

## 参考资料

1. [Tomcat](https://tomcat.apache.org/)
2. [从源码理解SpringBootServletInitializer的作用](https://blog.csdn.net/qq_43799161/article/details/125315579)
3. [使用 Docker 部署 Tomcat](https://blog.csdn.net/jks212454/article/details/130672006)
