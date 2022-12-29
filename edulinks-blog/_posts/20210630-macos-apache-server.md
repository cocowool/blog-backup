---
title: 探索macOS自带的apache
date: 2021-06-29 08:11:39
tags:
keywords: macOS, Mac, apache
description: 本文将macOS下自带的Apache服务器相关的信息和操作整理出来，方便大家查看使用。
---

> 写本文时使用的操作系统版本为macOS Catalina 10.15.7

前一段时间在配置macOS的PAC代理设置时（可以参考[macOS使用自动代理](http://www.edulinks.cn/2021/06/10/20210610-macos-proxy-auto-config/)），用到了macOS自带的Apache服务器。macOS自带了Apache服务器，提供HTTP服务，对于简单的日常使用或开发调试，非常方便。有时我们会需要修改一些配置，例如端口等，来避免和我们的其他环境冲突。本文就是因为占用了默认的80端口，导致我的一个测试例子报端口冲突，从而起了修改相关配置的念头。

本文将macOS下自带的Apache服务器相关的信息和操作整理出来，方便大家查看使用。

## 相关路径

* 配置文件路径 `/etc/apache2`

  ```sh
  ➜  / ll /etc/apache2
  total 96
  drwxr-xr-x   9 root  wheel   288B  4 18  2020 .
  drwxr-xr-x  84 root  wheel   2.6K  6 29 19:51 ..
  drwxr-xr-x  14 root  wheel   448B  6 10 08:51 extra
  -rw-r--r--   1 root  wheel    21K  4 18  2020 httpd.conf
  -rw-r--r--   1 root  wheel    13K  4 18  2020 magic
  -rw-r--r--   1 root  wheel    60K  4 18  2020 mime.types
  drwxr-xr-x   4 root  wheel   128B  4 18  2020 original
  drwxr-xr-x   4 root  wheel   128B  4 18  2020 other
  drwxr-xr-x   2 root  wheel    64B  4 18  2020 users
  ```

* 默认根文件夹 `/Library/WebServer/Documents`

## 服务起停

* 启动服务 `sudo apachectl start`

* 重启服务 `sudo apachectl restart`

* 停止服务 `sudo apachectl stop`

* 查看版本

  ```sh
  ➜  / sudo apachectl -v              
  Password:
  Server version: Apache/2.4.41 (Unix)
  Server built:   Jun  5 2020 23:42:06
  ```

## PHP支持

macOS还自带了PHP7的支持，需要通过修改 `/etc/apache2/httpd.conf` 配置文件打开并重启服务后生效。

```conf
#LoadModule php7_module libexec/apache2/libphp7.so  
```



## 参考资料

1. [macOS Apache](https://www.sqlsec.com/2017/07/macapache.html)
2. [Mac OS X为何自带Apache和PHP](https://www.zhihu.com/question/19849652/answer/23500445)
3. [详解Mac自带apache配置](https://www.jb51.net/article/115272.htm)



