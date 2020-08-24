---
title: Fedora10下配置Apache和虚拟主机
date: 2009-01-19 14:26:01
tag: 
---

起因：
最近配置Fedora下的虚拟主机时，配置在/home/user/下的文件总是不能够正常的访问，查看日志提示。
[Mon Jan 19 09:05:14 2009] [error] [client 127.0.0.1] (13)Permission denied: access to / denied

看来还是有权限的问题，找了一些，终于解决掉了，下面总结一下。
总结一下Fedora10下面Apache的配置问题：
1、安装apache。
Fedorar下安装apache如今已经很方便了，可以通过rpm或者yum进行安装，这里不再赘述。
2、安装PHP。
这一步也很简单，直接yum就可以了。
3、配置虚拟主机。
虚拟主机的配置可以写在httpd.conf中，也可以在/conf.d/下写入单独的文件，我是写在了/conf.d/vhost.conf中。
<VirtualHost *:80>
ServerAdmin cocowool@gmail.com
DocumentRoot /home/cipher/workspace
ServerName www.work.cn
ErrorLog logs/www.work.cn-error_log
CustomLog logs/www.work.cn-access_log common

</VirtualHost>
4、设置HOST。
需要根据自己的配置，更改/etc/hosts文件。
如：127.0.0.1　　www.work.cn

5、配置目录权限。
不能访问的主要原因就是权限的问题，目录权限是一个方面，所以需要对工作目录设置权限。
su -

chomd 711 /home/cipher
chmod 755 /home/cipher/workspace

6、SELINUX。
关于SELinux，我的了解还很少，而且也没有使用。所以直接禁止掉。
如果在系统中使用SELinux的话，需要设置下面的两个选项
setseool -P httpd_enbale_homedirs 1

chcon -R -t httpd_sys_content_t /home/cipher/workspace

最后重启服务就可以了。


-----------

一些补充
[warn] NameVirtualHost *:80 has no VirtualHosts

如果在启动apache的时候看到这个错误的话，是说明NameVirtualHost *:80写的太多了，所有的虚拟主机只需要一行就可以了
最后我们可以通过 httpd -S 来查看当前的运行状况。














