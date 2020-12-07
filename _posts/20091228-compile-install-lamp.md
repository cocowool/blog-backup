---
title: 在Centos 5.2下编译安装LAMP
date: 2009-12-28 16:27:01
tag: 
keywords: LAMP，Centos，编译安装，Linux
description: 本文介绍编译安装LAMP环境的方法，包括Apache、Mysql、PHP，基于虚拟主机在本地演示，同样适用于生产环境。
---

> 本文写于2009年，可能因技术发展，导致相应操作流程在新版本的环境中无法正常执行，欢迎大家通过公众号与我联系分析，不断完善本文。

首先使用 Virtualbox 安装一台 CentOS 5.2 的虚拟机，网络连接采用 Host-only Adapter，这样主客机之间可以互相访问，但是客机不能够上广域网。

## 系统初步配置
源文件位置    /root/software
编译安装位置    /usr/local/{software_name}
数据存放        /data1/
Apache运行账户    www:www
mysql运行账户    mysql:mysql
关闭 Selinux
Selinux 经常会对系统的运行产生一些奇怪的影响，如果不是特别熟悉的话，建议关掉 Selinux。
Selinux 的配置文件位于 /etc/selinux/config，我们需要修改的部分在：
SELINUX=enforcing
修改为    SELINUX=disabled，然后重启服务器，SELINUX 的配置救生效了。
配置 iptables
安装必要的软件
如果能够连上网络，可以通过 yum 来安装这些必要的工具；如果不能连上网络，则需要使用本地的安装源或者安装文件。

下面列出一些必须的工具：
[gcc](http://gcc.gnu.org/mirrors.html)
wget
make
ntp
patch
autoconf
automake
bzip2-devel

通过本地的源来进行安装，首先在 Virtualbox 的 Devices -> Mount CD/DVD-Rom -> CD/DVD-Rom image，加载安装的镜像文件。
计划安装的软件及其版本：
[apache 1.3.41](http://apache.freelamp.com/httpd/apache_1.3.41.tar.gz)
[cronolog 1.6.2](http://cronolog.org/download/cronolog-1.6.2.tar.gz)
[gd 2.0.35](http://www.libgd.org/releases/gd-2.0.35.tar.gz)
[libiconv 1.13.1](http://ftp.gnu.org/pub/gnu/libiconv/libiconv-1.13.1.tar.gz)
[libmcrypt 2.5.8](http://jaist.dl.sourceforge.net/sourceforge/mcrypt/libmcrypt-2.5.8.tar.bz2)
[mcrypt 2.6.7](http://jaist.dl.sourceforge.net/sourceforge/mcrypt/mcrypt-2.6.7.tar.gz)
[openssl 0.9.8k](http://www.openssl.org/source/openssl-0.9.8k.tar.gz)
[openssh    5.2p1](http://ftp.jaist.ac.jp/pub/OpenBSD/OpenSSH/portable/openssh-5.2p1.tar.gz)
[pecre 7.9](http://downloads.sourceforge.net/project/pcre/pcre/7.9/pcre-7.9.tar.gz?use_mirror=jaist)
[nginx 0.7.62](http://sysoev.ru/nginx/nginx-0.7.62.tar.gz)
[mysql 5.0.51](http://mysql.byungsoo.net/Downloads/MySQL-5.0/mysql-5.0.51b.tar.gz)
[php 5.2.9/4.4.9](http://au.php.net/distributions/php-5.2.9.tar.gz)
[memcached 1.4.1](http://memcached.googlecode.com/files/memcached-1.4.1.tar.gz)
[memcache 2.2.5](http://pecl.php.net/get/memcache-2.2.5.tgz)
[xcache 1.2.2](http://xcache.lighttpd.net/pub/Releases/1.2.2/xcache-1.2.2.tar.gz)
[zend optimizer 3.3.9](http://downloads.zend.com/optimizer/3.3.9/ZendOptimizer-3.3.9-linux-glibc23-i386.tar.gz)
[imap](ftp://ftp.cac.washington.edu/mail/imap.tar.Z)
[bind 9.4.2](http://ftp.isc.org/isc/bind9/9.4.2/bind-9.4.2.tar.gz)

## 安装其他软件
安装 OpenSSL：
./config --prefix=/usr/local/openssl/
make
make test
make install
安装GD2
ar jxvf gd-2.0.35.tar.bz2
cd gd-2.0.35
./configure --prefix=/usr/local/gd2
make &&make install
cd ..
安装libiconv
tar zxvf libiconv-1.12.tar.gz
cd libiconv-1.12
./configure --prefix=/usr &&make &&make install
cd ..
安装libmcrypt
tar jxvf libmcrypt-2.5.8.tar.bz2
cd libmcrypt-2.5.8
./configure --prefix=/usr/local/libmcrypt &&make &&make install
cd ..
安装 cronolog
./configure --prefix=/usr/local/cronolog
make && make install

## 安装mysql

本文采用的是二进制安装的方式进行的，基本的顺序和 mysql 官方的顺序差不多，只不过有些地方做一些说明。
shell> groupadd mysql
shell> useradd -g mysql mysql
shell> cd /usr/local
shell> tar -xvzf /path/to/mysql-VERSION-OS.tar.gz
shell> mkdir /usr/local/mysql
shell> cp -rf /path/to/mysql-VERSION-OS /usr/local/mysql
shell> cd /usr/local/mysql
shell> chown -R mysql .
shell> chgrp -R mysql .
shell> scripts/mysql_install_db --user=mysql
shell> chown -R root .
shell> chown -R mysql data
shell> bin/mysqld_safe --user=mysql &
前面几步一般不会有问题，只是最后一步执行 bin/mysqld_safe --user=mysql & 的时候，系统出错了：
Starting mysqld daemon with databases from /var/lib/mysql
STOPPING server from pid file /var/run/mysqld/mysqld.pid
090930 12:19:32  mysqld ended
然后就结束了，这个问题的原因，我们可以在日志文件 /var/log/mysqld.log 中看到
090930 12:19:31  mysqld started
090930 12:19:31 [Warning] option 'thread_stack': unsigned value 126976 adjusted to 131072
090930 12:19:32  InnoDB: Started; log sequence number 0 43655
090930 12:19:32 [ERROR] /usr/local/mysql/bin/mysqld: Can't create/write to file '/var/run/mysqld/mysqld.pid' (Errcode: 2)
090930 12:19:32 [ERROR] Can't start server: can't create PID file: No such file or directory
090930 12:19:32  mysqld ended
也就是权限不够，解决的办法有两个，一个是建立一个 mysql 有权限读写的 /var/run/mysqld/ 文件夹；另一个方法，我们可以使用 /usr/local/mysql/support-files/mysql.server 来进行服务的启动。
这样 mysql 安装完成以后，文件的存放结构都是按照默认的设定来的，比如一些常见的：
配置文件    /etc/my.cnf
basedir    /usr/local/mysql
最后需要配置一下mysql随系统启动服务，拷贝 /usr/local/mysql/support-files/mysql.server -> /etc/rc.d/init.d/mysql 中，然后添加 chkconfig --add mysql
这样就可以通过 service mysql start 来启动 mysql 服务了

## 安装Apache

这篇里 apache 选择经典的 1.3.41 版本。
apache 的安装方法不止一种，无论哪一种，首先我们必须确认当前系统中没有 httpd 服务正在运行：ps -aux | grep httpd，如果我们发现结果中有的话，应该将他杀掉。
然后到 apache 的安装目录下，这里是 /root/software/source_lamp/apache_1.3.41，整个目录内的结构和文件介绍如下：
cgi-bin/ ――用于测试CGI的脚本。随后，安装过程将把这些文件复制到服务器的/cgi-bin目录中。如果启动了Apache的CGI功能，这些脚本可以用于测试那些功能。
conf/ ――配置文件。这些发布时的配置不应该进行编辑。安装过程把这些文件复制到服务器的配置目录中，对这些复制文件将进行扩展编辑。
htdocs/ ――第一个Web站点。安装过程把这些文件复制到服务器的文档根目录中。浏览器第一次测试服务器时将显示这些网页。
icons/ ――目录索引图标。这些图像文件将成为由Web服务器用来提供特别路径索引功能的图标。
logs/ ――日志工具。这个目录是空的，安装过程将为安装好的服务器创建一个日志目录。
src/ ――源代码。这个目录（和它的子目录）包含了C语言源代码文件，你编译它们以创建出Apache Web服务器。
首先执行 configure，这里将 bin/data/log，三个目录进行了自定义：
./configure --prefix=/usr/local/lampsrv/apache/ --datadir=/data1/www/data/htdocs/ --logfiledir=/data1/www/applogs/ --enable-shared=max --enable-module=most
make
make install

## 安装PHP

解压下载的安装包，然后执行配置。
./configure --prefix=/usr/local/lampsrv/php --with-apxs=/usr/local/lampsrv/apache/bin/apxs  --with-config-file-path=/usr/local/lampsrv/etc/php/ --enable-track-vars --with-xml --with-mysql
make && make install
安装完成后，在 apache 的配置文件中添加对于 php 后缀的解析。
AddType application/x-httpd-php .php .php3 .inc
AddType application/x-httpd-php-source .phps
然后重启 apache 服务:/usr/local/lampsrv/apache/bin/apachectl restart
启动的时候，有可能会遇到这样的错误：Cannot load /usr/local/apache2/modules/libphp4.so into server: /usr/local/apache2/modules/libphp4.so : cannot restore segment prot after reloc: Permission Denied
这种情况一般是由于 selinux 的安全设置引起的，可以考虑关闭掉 selinux，在 /etc/selinux/config 中进行设置。

## 安装总结

至此，我们已经配置了基本的 Linux + Apache + Mysql + PHP 的运行环境，但是仍有很多的配置文件散落在各处，比如 mysql 的配置文件和数据文件。如何将这些配置文件和数据文件归拢在一个统一的位置，比如 /data1/www/etc ，还是一个需要不断总结积累的问题，另外还有一个就是如何形成一个快速安装配置的模式，是通过编写脚本还是通过搭建 yum 源来实现多台机器的快速配置是一个很需要经验的问题，在这两个方面，今后要不断的总结。

## 参考资料：

1. CentOS 5.2 编译安装](http://www.linuxsky.org/doc/network/200811/345.html)
2. [Apache httpd project](http://httpd.apache.org/)
3. [Cronolog](http://cronolog.org/)
4. [GD Library](http://www.libgd.org/Main_Page)
5. [OPEN SSL](http://www.openssl.org/)
6. [OPEN SSH](http://www.openssh.com/)
7. [PCRE](http://www.pcre.org/)
8. [nginx](http://nginx.net/)
9. [mysql](http://www.mysql.com)
10. [php](http://www.php.net/)
11. [Memcached](http://www.danga.com/memcached/)
12. [XCache](http://xcache.lighttpd.net/)
13. [IMAP](http://www.imap.org/)
14. [Mysql Install](http://dev.mysql.com/doc/refman/5.0/en/installing.html)
15. [Linux自启动详细设置](http://blogger.org.cn/blog/more.asp?name=zhanglincon&id=32410)
16. [Apache 1.3.x 安装配置笔记](http://www.chedong.com/tech/apache_install.html)
17. [Apache 安装指南](http://www.chinaunix.net/jh/13/49365.html)












