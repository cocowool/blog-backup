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

同时需要修改入口类 App.java 。
```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.boot.builder.SpringApplicationBuilder;
import org.springframework.boot.web.servlet.support.SpringBootServletInitializer;

/**
 * Hello world!
 *
 */
@SpringBootApplication
public class App extends SpringBootServletInitializer {
    public static void main( String[] args )
    {

        System.out.println( "Hello World!" );
        SpringApplication.run(App.class,args);
    }

    @Override
    protected SpringApplicationBuilder configure(SpringApplicationBuilder builder){
        return builder.sources(App.class);
    }
}
```

本文选择使用 Docker 运行 Tomcat，使用的 Tomcat 版本为 9.0.31 ，具体的操作过程如下：
```
# 使用 mvn 生成 war 包
$ mvn package
# 将生成的 war 包考入一个目录，把这个目录映射为容器 tomcat 的 webapps 目录
$ docker run --rm --name my_tomcat -p 8080:8080 -v /Users/shiqiang/Projects/sh-valley/docker-conf/tomcat/data:/usr/local/tomcat/webapps/ tomcat:9.0.31
```

容器启动后通过 http://localhost:8080/war 包名称 可以访问 springboot 应用。

## Docker 方式部署

可以将我们之前编写的简单 springboot 应用程序构建为一个应用镜像，这种部署方式需要有容器环境。采用这种方式将为运维人员提供很好的操作管理能力。

将之前打包生成的 springboot jar 文件拷贝入一个空白的文件夹，并创建一个 Dockerfile，Dockerfile 的具体内容如下：

```Dockerfile
# 使用Java 8为基础镜像                                                     FROM openjdk:8 
  
# 维护者信息  
MAINTAINER cocowool<cocowool@qq.com>  
  
# 指定临时文件目录为/tmp  
VOLUME /tmp  
  
# 将jar包添加到容器中并更名为app.jar  
ADD patrolselfservice-1.0-SNAPSHOT.jar app.jar  
  
# 运行jar包  
RUN bash -c 'touch /app.jar'  
  
# 暴露容器的8080端口  
EXPOSE 8080  
  
# 设置容器启动时的入口点  
ENTRYPOINT ["java", "-jar", "/app.jar", "-Djava.security.egd=file:/dev/./urandom", "--spring.profiles.active=test", "--server.port=8080", "> /log/app.log"]
```

确认一下目录结构
```sh
$ ls -lh 
total 34488
-rw-r--r--  1 shiqiang  staff   561B Nov 19 21:40 Dockerfile
-rw-r--r--  1 shiqiang  staff    17M Nov 18 16:01 patrolselfservice-1.0-SNAPSHOT.jar
```

然后执行镜像创建的命令 `docker build -t springbootapp:0.0.1 .`。等命令执行结束后，就可以使用 docker 运行自己的 springboot 应用。

```sh
$ docker run --rm --name my_springboot -p 8080:8080 springbootapp:0.0.1
```

## 参考资料

1. [Tomcat](https://tomcat.apache.org/)
2. [从源码理解SpringBootServletInitializer的作用](https://blog.csdn.net/qq_43799161/article/details/125315579)
3. [使用 Docker 部署 Tomcat](https://blog.csdn.net/jks212454/article/details/130672006)
4. [https://blog.csdn.net/prxhlirr/article/details/126398361](https://blog.csdn.net/prxhlirr/article/details/126398361)
