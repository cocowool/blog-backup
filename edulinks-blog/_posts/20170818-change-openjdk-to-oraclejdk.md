---
title: 将Linux默认的OpenJDK替换为Oracle JDK
date: 2017-08-18 09:08:01
tag: 
keywords: linux, openjdk, oracle jdk
description: Logstash安装插件的时候，发生与系统自带的OpenJDK有关的错误，解决方案是将系统自带的OpenJDK更新为Oracle的JDK。
---

在使用Logstash安装插件的时候，发生了一个错误，如下：
```bash
ERROR: Something went wrong when installing logstash-input-jdbc, message: java.security.KeyException
```
经查可能是与系统自带的OpenJDK有关，解决方案是将系统自带的OpenJDK更新为Oracle的JDK。
```bash
[cloud@localhost ~]$ java -version
openjdk version "1.8.0_141"
OpenJDK Runtime Environment (build 1.8.0_141-b16)
OpenJDK 64-Bit Server VM (build 25.141-b16, mixed mode)
```

### 下载 Oracle JDK
首先从[Oracle网站](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)下载所需的JDK。
我下载的是 1.8 版本的 rpm 安装包，因为 Elasticsearch 套装需要Java 1.8。

### 安装Oracle JDK
使用如下命令进行安装
```bash
[root@localhost cloud]# rpm -ivh jdk-8u144-linux-x64.rpm 
Preparing...                          ################################# [100%]
Updating / installing...
   1:jdk1.8.0_144-2000:1.8.0_144-fcs  ################################# [100%]
Unpacking JAR files...
    tools.jar...
    plugin.jar...
    javaws.jar...
    deploy.jar...
    rt.jar...
    jsse.jar...
    charsets.jar...
    localedata.jar...
```

### 使用update-alternative配置java
首先执行```update-alternative —list java```看一下系统中有几个java版本。
```bash
[root@localhost cloud]# update-alternatives --display java
java - status is auto.
 link currently points to /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.141-1.b16.el7_3.x86_64/jre/bin/java
/usr/lib/jvm/java-1.7.0-openjdk-1.7.0.51-2.4.5.5.el7.x86_64/jre/bin/java - priority 170051
 slave ControlPanel: (null)
 slave javaws: (null)
 slave jcontrol: (null)
 slave jjs: (null)
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.141-1.b16.el7_3.x86_64/jre/bin/java - family java-1.8.0-openjdk.x86_64 priority 1800141
 slave ControlPanel: (null)
 slave javaws: (null)
 slave jcontrol: (null)
 slave jjs: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.141-1.b16.el7_3.x86_64/jre/bin/jjs
/usr/java/jdk1.8.0_144/jre/bin/java - priority 180144
 slave ControlPanel: /usr/java/jdk1.8.0_144/jre/bin/ControlPanel
 slave javaws: /usr/java/jdk1.8.0_144/jre/bin/javaws
 slave jcontrol: /usr/java/jdk1.8.0_144/jre/bin/jcontrol
 slave jjs: /usr/java/jdk1.8.0_144/jre/bin/jjs
Current `best' version is /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.141-1.b16.el7_3.x86_64/jre/bin/java.
```
输出的内容省略了一部分，可以看到，系统中存在OpenJDK 和 Oracle的JDK，下面选择Oracle JDK为默认
```bash
[root@localhost cloud]# update-alternatives --config java

There are 3 programs which provide 'java'.

  Selection    Command
-----------------------------------------------
   1           /usr/lib/jvm/java-1.7.0-openjdk-1.7.0.51-2.4.5.5.el7.x86_64/jre/bin/java
*+ 2           java-1.8.0-openjdk.x86_64 (/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.141-1.b16.el7_3.x86_64/jre/bin/java)
   3           /usr/java/jdk1.8.0_144/jre/bin/java

Enter to keep the current selection[+], or type selection number: 3
[root@localhost cloud]# java -version
java version "1.8.0_144"
Java(TM) SE Runtime Environment (build 1.8.0_144-b01)
Java HotSpot(TM) 64-Bit Server VM (build 25.144-b01, mixed mode)
```
update-alternatives 提供了交互界面，我们选择3之后会车，配置就会生效，可以看到已经改过来了。

### update-alternatives 介绍
如果我们用```ls -l /usr/bin/java```查看，可以发现命令其实都是软链接，现在java会定期升级，版本也特别多，所以很多命令都是软链接，update-alternatives就是管理这些软链接，这样版本切换的时候，可以将所有的链接同时进行更新。
> 本文操作系统为 CentOS release 6.5(final)，本文是用 rpm 方式安装的jdk，如果下载的源码，可能需要配置 profile 并用 update-alternative —install 命令安装，本文没有尝试。

参考资料：
1、[linux下如何使用自己安装的SunJDK替换默认的OpenJDK](http://blog.csdn.net/rj042/article/details/72034650)
2、[如何更改Linux中默认的openjdk为自己安装的JDK](http://blog.csdn.net/kkdelta/article/details/50365323)
3、[Linux安装Oracle JDK替换OpenJDK详解](http://www.cnblogs.com/gavinyinyuan/p/4227720.html)












