---
title: Springboot 工程的配置集成
date: 2023-11-21 08:28:03
keywords: springboot, properties, yml, springboot 配置, springboot 工程, springboot 学习, springboot 入门
description: 本文介绍 Springboot 工程自定义配置文件的使用方法，记录相关学习心得体会。
---

Springboot 支持 properties 或者 yml 两种形式的配置文件。

## 自定义 properties 配置文件

配置文件有四个可以保存的位置：
* 项目的根目录下
* 项目根目录下的 config 文件夹
* 项目 src/main/resources 目录
* 项目 src/main/resources/config 目录

本工程基于一个简单的 springboot 启动程序作为开始，如果想了解如何初始化一个简单的 springboot 工程，可以参考 [Springboot 工程创建入门](http://www.edulinks.cn/2023/03/19/20230319-spring-boot-starter/) 这篇文章。在本工程中，我们创建以下的目录结构：
```sh
$ tree .                   
.
├── pom.xml
├── src
│   ├── main
│   │   ├── java
│   │   │   └── cn
│   │   │       └── edulinks
│   │   │           └── patrolselfservice
│   │   │               ├── App.java
│   │   │               ├── config
│   │   │               │   └── ServerConfig.java
│   │   │               └── controller
│   │   │                   └── ServerController.java
│   │   └── resources
│   │       ├── application.properties
│   │       ├── patrolserver.properties
│   └── test
│       └── java
│           └── cn
│               └── edulinks
│                   └── patrolselfservice
│                       └── AppTest.java
```
项目的入口类是 App.java，在 resources 文件夹下创建了 patrolserver.properties 配置文件，记录了一些服务器相关的属性，对应的建立了一个配置类 ServerConfig.java，并在 ServerController.java 控制类中使用了相关的配置属性。

## 区分测试和生产环境

Profile 的管理，配置文件指定，命令行指定


##  计划补充的几个系列
* log4j2 日志配置
* 数据库连接配置 / 多数据源配置
* REST 服务 / API文档 swagger
* 整合 Redis
* Springboot 应用的监控 / 可观测
* 认证 OAuth 2.0 和 JWT
* 整合 Kafka / ActiveMQ
* 批处理
* 定时任务 Quartz / xxljob

## 参考资料
1. [Springboot 配置文件格式](https://baijiahao.baidu.com/s?id=1766310050451174842&wfr=spider&for=pc)
2. Springboot 整合开发实战
3. [springboot基础系列-properties配置](https://www.cnblogs.com/V1haoge/p/7183408.html)
4. [SpringBoot加载自定义配置文件](https://blog.csdn.net/new_renren/article/details/130940498)