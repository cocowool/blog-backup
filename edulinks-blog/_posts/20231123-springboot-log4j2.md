---
title: Springboot 工程的日志配置
date: 2023-11-23 19:27:38
keywords: springboot, springboot 日志, springboot log4j2, springboot logback
description: 本文介绍如何在 Springboot 项目中集成使用 log4j2 日志框架。
---

> 本文基于 mvn 3.8.6，Springboot 

开发应用需要管理应用日志，记录应用程序运行时的各类输出信息，还要避免对于正常的业务逻辑产生影响，此时就需要使用一些成熟的日志框架，如 log4j、logback 等等。Log4j2 是一个 Java 日志框架，是 log4j 的升级版，提供了更快的处理速度和更丰富的日志记录功能，支持异步日志记录，减少了对性能的影响。

值得一提的是，log4j、logback 的作者都是 [Ceki Gülcü](https://github.com/ceki)，后来 Apache 基金会借鉴 logback 优秀的开发理念重新开发推出了  log4j 2.x 。可以说 Java 世界的日志库绕不开  [Ceki Gülcü](https://github.com/ceki) 。

本文以一个非常简单的 Springboot 项目来演示如何集成 log4j2 来输出应用日志。本文的工程代码可以在 [我的Github](https://github.com/cocowool) 上获取。

首先利用 mvn 来创建起步的工程框架。
```
$ mvn archetype:generate -DgroupId=cn.edulinks.springbootdemo -DartifactId=springboot-demo-03 -DinteractiveMode=false
```

修改 pom.xml 文件，去掉对默认日志框架的使用，引入 log4j2 依赖包。
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-v4_0_0.xsd">
  <modelVersion>4.0.0</modelVersion>
  <groupId>cn.edulinks.springbootdemo</groupId>
  <artifactId>springboot-demo-03</artifactId>
  <packaging>jar</packaging>
  <version>1.0-SNAPSHOT</version>
  <name>springboot-demo-03</name>
  <url>http://maven.apache.org</url>
  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.2.5.RELEASE</version>
  </parent>
  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
      <version>3.0.4</version>
      <exclusions>
        <exclusion>
          <groupId>org.springframework.boot</groupId>
          <artifactId>spring-boot-starter-logging</artifactId>
        </exclusion>
      </exclusions>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-log4j2</artifactId>
      <version>2.2.5.RELEASE</version>
    </dependency>
    <dependency>
      <groupId>junit</groupId>
      <artifactId>junit</artifactId>
      <version>3.8.1</version>
      <scope>test</scope>
    </dependency>
  </dependencies>
  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

在 `src/main/resources` 文件夹下创建 log4j2.xml 和 application.properties 两个配置文件。

log4j2.xml 的内容
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<configuration status="warn" monitorInterval="5">
    <properties>
        <!--日志文件的保存目录-->
        <property name="logDir">./logs</property>
        <property name="splitSize">5MB</property>
        <property name="fileCount">10</property>
        <property name="logPattern">%d{yyyy-MM-dd HH:mm:ss.SSS} => [%thread] => %-5level %logger{50}:(%line) - %msg%n</property>
    </properties>
    <Appenders>
        <File name="File" fileName="${logDir}/app-log.log">
            <PatternLayout pattern="[%d{yyyy-MM-dd HH:mm:ss}] [%-5level] %l %c{36} - %m%n"/>
        </File>
    </Appenders>
    <loggers>
        <Root level="DEBUG">
            <AppenderRef ref="File" />
        </Root>
    </loggers>
</configuration>
```

application.properties 是工程默认的配置文件，这里只配置制定了 log4j2 的配置文件。
```properties
logging.config = classpath:log4j2.xml
```

在工程的 App.java 中输出了 debug 和 info 级别的日志
```java
package cn.edulinks.springbootdemo;


import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class App
{

    private static final Logger LOG = LoggerFactory.getLogger(App.class);

    public static void main( String[] args )
    {
        System.out.println( "Hello World!" );
        LOG.debug("this is debug message ... ");
        LOG.info("this is debug message ... ");
        System.out.println( "Hello World Again!" );

    }
}
```

最后编译运行项目，就能够看到在项目文件夹下创建了 logs 目录并且生成了日志文件。
```sh
$ mvn package
$ java -jar target/springboot-demo-03-1.0-SNAPSHOT.jar
Hello World!
Hello World Again!
$ cat logs/app-log.log
[2023-12-06 19:04:44] [DEBUG] cn.edulinks.springbootdemo.App.main(App.java:24) cn.edulinks.springbootdemo.App - this is debug message ... 
[2023-12-06 19:04:44] [INFO ] cn.edulinks.springbootdemo.App.main(App.java:25) cn.edulinks.springbootdemo.App - this is debug message ... 
```

## 参考资料
1. [深入理解 SpringBoot 日志框架：从入门到高级应用](https://blog.csdn.net/qq_20185737/article/details/131252893)
2. [log4j 作者的 Github](https://github.com/ceki)
3. [日志库的历史和体系](https://www.jianshu.com/p/b14fe2e1949e)
4. [log4j2 documentation](https://logging.apache.org/log4j/2.x/index.html)