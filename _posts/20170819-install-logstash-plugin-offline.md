---
title: 如何进行Logstash logstash-input-jdbc插件的离线安装
date: 2017-08-19 13:31:01
tag: 
keywords: logstash, logstash-input-jdbc插件, 离线安装
description: 我们单位的服务器位于隔离区，不允许链接互联网，因此整理了在ELK集群上离线安装Logstash的jdbc input插件的方法，供大家参考。
---

我们单位的服务器位于隔离区，不允许链接互联网，因此整理了在ELK集群上离线安装Logstash的jdbc input插件的方法，供大家参考。
> 总体思路是需要一台中转的机器，这台机器需要能够访问互联网，先在这台机器中将需要安装的插件及依赖包制作成离线安装包，然后再拷贝到生产机器上安装。


### 打包
打包前的注意事项
**1、确保需要打包的插件及其依赖插件都已经安装在中转机器上**
**2、执行```./logstash-plugin prepare-offline-pack logstash-input-jdbc```**来打包
打包命令支持通配符，如下都是可以的
```bash
bin/logstash-plugin prepare-offline-pack logstash-input-jdbc 
bin/logstash-plugin prepare-offline-pack logstash-input-* 
bin/logstash-plugin prepare-offline-pack logstash-output-* logstash-input-jdbc
```

### 安装
**1、下载打包好的文件，通过你最方便的方式上传到生产设备中，记住存放的目录和文件名，例如这里为logstash-input-plugins-5.5.1.zip**
**2、执行```bin/logstash-plugin install```命令进行安装**
在Windows下
```bash
bin/logstash-plugin.bat install file:///c:/path/to/logstash-input-plugins-5.5.1.zip
```
在Linux下
```bash
bin/logstash-plugin install file:///path/to/logstash-offline-input-5.5.1.zip
```
> 本文所用的版本为 Logstash 5.5.1。

参考资料：
1、[Offline Plugin Management](https://www.elastic.co/guide/en/logstash/current/offline-plugins.html)












