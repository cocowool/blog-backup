---
title: Springboot 工程的配置集成
date: 2023-11-21 08:28:03
keywords: springboot, properties, yml, springboot 配置, springboot 工程, springboot 学习, springboot 入门
description: 本文介绍 Springboot 工程自定义配置文件的使用方法，记录相关学习心得体会。
---

Springboot 支持 properties 或者 yml 两种形式的配置文件，工程默认支持 application.properties 和 application.yml 两个配置文件，这个配置文件中的内容会自动加载。如果需要创建自定义的配置文件并实现加载，需要按照下面步骤操作。需要注意，自定义配置文件无法实现自动加载，需要手工加载。

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

patrolserver.properties 配置文件代码如下:
```
patrolserver.ip = 127.0.0.1
patrolserver.hostname = mytestserver
patrolserver.status = running
patrolserver.port = 3381
```

ServerConfig.java 配置类代码如下
```java
package cn.edulinks.patrolselfservice.config;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;

@Component
@PropertySource(value = {"classpath:patrolserver.properties"})
@ConfigurationProperties(prefix = "patrolserver")
public class ServerConfig {
    private String ip;
    private String hostname;
    private String status;
    private String port;

    @Override
    public String toString(){
        return "PatrolServer { ip = " + ip + ", hostname = " + hostname + ", status = " + status + ", port = " + port + "}";
    }

    public String getIp() {
        return ip;
    }

    public String getHostname() {
        return hostname;
    }

    public String getStatus() {
        return status;
    }

    public String getPort() {
        return port;
    }

    public void setIp(String ip) {
        this.ip = ip;
    }

    public void setHostname(String hostname) {
        this.hostname = hostname;
    }

    public void setStatus(String status) {
        this.status = status;
    }

    public void setPort(String port) {
        this.port = port;
    }
}
```

ServerController.java 控制类代码如下：
```java
package cn.edulinks.patrolselfservice.controller;

import cn.edulinks.patrolselfservice.config.ServerConfig;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@RequestMapping("/server")
public class ServerController {

		//通过 Value 注解获取单个配置属性
    @Value("${patrolserver.hostname}")
    private String hostname;

		//通过配置类批量获取配置数据
    @Resource
    private ServerConfig serverConfig;

    @RequestMapping(value = "/all", method = RequestMethod.GET)
    public String getServer(){
        System.out.println("server.host = " + serverConfig.getIp());

        return serverConfig.toString();
    }

}
```

编译运行后访问 /server/all 可以看到如下输出
```html
PatrolServer { ip = 127.0.0.1, hostname = mytestserver, status = running, port = 3381}
```

## 自定义 yml 配置文件

默认 ProperySource 不支持自定义的 yml 配置文件加载，需要编写一个单独的类，这里搬运了[蚩尤后裔](https://wangmaoxiong.blog.csdn.net/?type=blog) 提供的版本。

```java
package jh.yysjzx.patrolselfservice;

import org.springframework.boot.env.YamlPropertySourceLoader;
import org.springframework.core.env.PropertySource;
import org.springframework.core.io.support.DefaultPropertySourceFactory;
import org.springframework.core.io.support.EncodedResource;
import java.io.IOException;
import java.util.List;

/**
 * 用于  @PropertySource 加载 yml 配置文件.
 *
 * @author wangmaoxiong
 * @version 1.0
 * @date 2020/5/25 20:45
 */
public class PropertySourceFactory extends DefaultPropertySourceFactory {
    @Override
    public PropertySource<?> createPropertySource(String name, EncodedResource resource) throws IOException {
        if (resource == null) {
            return super.createPropertySource(name, resource);
        }
        List<PropertySource<?>> sources = new YamlPropertySourceLoader().load(resource.getResource().getFilename(), resource.getResource());
        return sources.get(0);
    }
}
```

参考 ServerConfig.java 配置类，在 config 文件夹下创建 SubsystemConfig.java 类。
```java
package jh.yysjzx.patrolselfservice.config;

import jh.yysjzx.patrolselfservice.PropertySourceFactory;
import lombok.Data;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.context.properties.ConfigurationProperties;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.PropertySource;
import org.springframework.stereotype.Component;
//import java.lang.String;

@Component
@PropertySource(value = {"classpath:subsystem.yml"}, factory = PropertySourceFactory.class)
@ConfigurationProperties(prefix = "subsystem")
public class SubsystemConfig {

    private String sysname;
    private String sysowner;
    private String sysgroup;


    public String getSysname() {
        return sysname;
    }

//    @Autowired
    public void setSysname(String sysname) {
        this.sysname = sysname;
    }

    public String getSysowner() {
        return sysowner;
    }

//    @Autowired
    public void setSysowner(String sysowner) {
        this.sysowner = sysowner;
    }

    public String getSysgroup() {
        return sysgroup;
    }

//    @Autowired
    public void setSysgroup(String sysgroup) {
        this.sysgroup = sysgroup;
    }

    @Override
    public String toString(){
        return "Subsystem { sysname = " + sysname + ", sysowner = " + sysowner + ", sysgroup = " + sysgroup + "}";
    }
}
```

在 SystemController.java 类中调用使用的示例。
```java
package jh.yysjzx.patrolselfservice.controller;

import jh.yysjzx.patrolselfservice.config.ServerConfig;
import jh.yysjzx.patrolselfservice.config.SubsystemConfig;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.context.annotation.PropertySource;
import org.springframework.core.env.Environment;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
//@PropertySource(value = {"classpath:subsystem.yml"})
@RequestMapping("/system")
public class SystemController {
    private Environment env;

    @Resource
    private SubsystemConfig subsystemConfig;

    @Value("${patrolserver.hostname}")
    private String hostname;

    @Value("${server.port}")
    private String serverPort;

    @Value("${subsystem.sysname}")
    private String sysname;

    @RequestMapping(value="/info", method = RequestMethod.GET)
    public String getInfo(){
//        String hostname = env.getProperty("server.port");
        System.out.println("Here is system / info. ");
        System.out.println(hostname);
        System.out.println(serverPort);

        System.out.println(sysname);
        System.out.println(subsystemConfig);

        return "ok";
    }
}
```

## 多环境配置

Profile 的管理，配置文件指定，命令行指定


## 参考资料
1. [Springboot 配置文件格式](https://baijiahao.baidu.com/s?id=1766310050451174842&wfr=spider&for=pc)
2. Springboot 整合开发实战
3. [springboot基础系列-properties配置](https://www.cnblogs.com/V1haoge/p/7183408.html)
4. [SpringBoot加载自定义配置文件](https://blog.csdn.net/new_renren/article/details/130940498)
5. [Springboot Documentation](https://docs.spring.io/spring-boot/docs/current-SNAPSHOT/reference/htmlsingle/#features.external-config)
6. [Springboot 加载配置文件方式](https://blog.csdn.net/wangmx1993328/article/details/81005170/)