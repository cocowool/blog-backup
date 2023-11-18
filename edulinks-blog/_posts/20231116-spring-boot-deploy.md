---
title: Springboot 工程的部署方式
date: 2023-11-16 08:32:30
keywords: springboot, 部署
description: 本文介绍 Springboot 工程的几种部署方式，包括 Tomcat 部署和容器部署。
---

## War 包部署到外部 Tomcat

Tomcat 是一个免费开源的 Java Web 应用服务器，实现了 Java Servlet 和 JavaServer Pages 规范，属于轻量级应用服务器，没有商业中间件如 Weblogic、Websphere 等集群管理能力。在中小型系统被广泛使用，是Java Web开发调试的首选。

将 Springboot 工程使用 War 包方式部署，需要首先调整  pom 文件，将 jar 包改为 war 包，并且添加排除 tomcat 相关的配置。

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>jh.yysjzx.patrolselfservice</groupId>
  <artifactId>patrolselfservice</artifactId>
  <packaging>war</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>patrolselfservice</name>
  <url>http://maven.apache.org</url>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
  </parent>
  <dependencies>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <scope>provided</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
        <version>2.7.9</version>
      </plugin>
    </plugins>
  </build>
</project>
```

本文选择使用 Docker 运行 Tomcat，使用的 Tomcat 版本为 9.0.31 。
```
$ docker run --rm --name my_tomcat -p 8080:8080 -v /Users/shiqiang/Projects/sh-valley/docker-conf/tomcat/data:/usr/local/tomcat/webapps/ tomcat:9.0.31
```

## Docker 方式部署

## 参考资料

1. [Tomcat](https://tomcat.apache.org/)
2. [从源码理解SpringBootServletInitializer的作用](https://blog.csdn.net/qq_43799161/article/details/125315579)
3. [使用 Docker 部署 Tomcat](https://blog.csdn.net/jks212454/article/details/130672006)