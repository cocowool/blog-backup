---
title: Apache配置中的安全问题
date: 2021-05-20 08:36:43
tags:
keywords: apache, lamp, php, linux
description: 本文介绍生产中需要注意的一些Apache安全配置，帮助我们搭建更稳定的Web服务。
---

Apache是非常流行的Web服务器，近几年虽然Nginx大有取代之势，但Apache仍占有不少的使用量。本文介绍生产中需要注意的一些安全配置，帮助我们搭建更稳定的Web服务。

## **1、Apache的安全配置。**

1.1 禁止服务器广播敏感信息。

   将 ServerSignature 设置为 Off，避免暴露Apache软件的版本号等信息。

   禁用目录浏览选项，在Directory中，将 Indexes 去掉。

   禁用符号链接追踪功能，避免恶意用户通过符号链接访问系统文件。禁用 FollowSymLinks 选项。如有有些用户必须使用符号链接的功能，可以使用 SymLinksIfOwnerMatch 的选项。

1.2 Apache的运行用户

   最小权限原则。

   记住一定不要用 root 用户运行 Apache，使用 www 用户也并非十分安全，确保 Apache 运行在一个具有有限权限的用户下。

   一般情况下，最好让Apache运行在一个单独的用户和用户组下，该用户一定不能具有SSH的登录权限。

1.3 只安装所需要的功能模块

   有一些默认打开的模块，使用频率很低，可以关掉，避免不必要的危险：userdir、autoindex、status、env、setenvif、cgi、actions、negotiation、include、filter、version等。

   安装Apache后，可以通过./httpd -l 查看安装的模块。

   如果不需要安装，在编译阶段可以通过configure命令指定。

1.4 为conf和bin文件夹设置合适的权限。

   一般情况下，只有系统管理员才能够对conf文件进行查看和修改，执行bin下的命令，应该禁止管理员以外的用户查看这些文档。

1.5 禁止.htaccess

   在目录的配置中，添加 AllowOverride None，例如：
```conf
   <Directory />
     Options None
     AllowOverride None
     Order allow,deny
     Allow from all
   </Directory>
```

## **2、PHP的安全配置。**

2.1 隐藏PHP。

   expose_php = Off

2.2 关闭错误提示

   display_errors = Off

2.3 禁用危险的系统函数

   disable_functions = system,exec,shell_exec,passthru,popen,fsockopen,chmod,rmdir,opendir

   这个选项只能在 php.ini 中配置，无法在单独的虚拟机上配置

2.4 限制可以打开的目录

   这个配置的目的相当于为项目创建一个沙盒。

   在 php.ini 中配置 open_basedir 参数

## **3、其他方法**

3.1 勤打补丁

   定期查看是否有软件的升级补丁，发生紧急漏洞修复补丁时，在第一时间为自己的系统安装补丁。

## 参考资料：

1、[Apache安全配置](http://www.itlearner.com/article/4347)
2、PHP手册
3、[Apache安全设置](http://www.blogjava.net/bukebushuo/articles/293776.html)
4、An Apache Trick to Mitigate Shell File Attack
5、[构建安全的Apache+Mysql+PHP的Web服务器](http://ggmmchou.blog.163.com/blog/static/59333149201161501049820/)
6、[Apache安全](http://www.blogjava.net/bukebushuo/articles/293776.html)设置
7、[10 Tips to Secure Your Apache Web Server on Linux/Unix](http://www.thegeekstuff.com/2011/03/apache-hardening/)