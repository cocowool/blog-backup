---
title: Springboot 工程的日志配置
date: 2023-11-23 19:27:38
keywords: springboot, springboot 日志, springboot log4j2, springboot logback
description: 
---

> 本文基于 mvn 3.8.6，Springboot 

开发应用需要管理应用日志，记录应用程序运行时的各类输出信息，还要避免对于正常的业务逻辑产生影响，此时就需要使用一些成熟的日志框架，如 log4j、logback 等等。Log4j2 是一个 Java 日志框架，是 log4j 的升级版，提供了更快的处理速度和更丰富的日志记录功能，支持异步日志记录，减少了对性能的影响。

值得一提的是，log4j、logback 的作者都是 [Ceki Gülcü](https://github.com/ceki)，后来 Apache 基金会借鉴 logback 优秀的开发理念重新开发推出了  log4j 2.x 。可以说 Java 世界的日志库绕不开  [Ceki Gülcü](https://github.com/ceki) 。



```
$ mvn archetype:generate -DgroupId=cn.edulinks.springbootdemo -DartifactId=springboot-demo-03 -DinteractiveMode=false
```

## 参考资料
1. [深入理解 SpringBoot 日志框架：从入门到高级应用](https://blog.csdn.net/qq_20185737/article/details/131252893)
2. [log4j 作者的 Github](https://github.com/ceki)
3. [日志库的历史和体系](https://www.jianshu.com/p/b14fe2e1949e)
4. [log4j2 documentation](https://logging.apache.org/log4j/2.x/index.html)

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

