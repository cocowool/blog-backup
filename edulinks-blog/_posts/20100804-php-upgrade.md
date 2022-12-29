---
title: CentOs5.2中PHP的升级
date: 2010-08-04 15:17:01
tag: 
keywords: php, php升级, centos php
description: CentOS下自带PHP的升级方案。
---

最近一个项目中需要使用到PHP5.2的版本，而服务器上使用了官方的yum源进行安装，默认的版本是5.1.6，需要升级。但是因为不是一个非常正式的服务器环境，所以想通过简单的yum update一下了事。但是很不幸,CentOS的官方默认YUM源不提供直接的升级，因为CentOS一向以稳定著称，所以在他的源里面，提供的一般都是经过千锤百炼的版本。

但是CentOS并不是裹足不前的，他同时提供了一个开发源，通过这个开发源，我们可以轻松的安装一些比较新潮的版本。

在系统中添加开发源的方法比较简单，下面是具体步骤：

1、首先创建一个单独的源文件
```sh
# /etc/yum.repos.d/CentOS-Testing.repo
```

2、在文件中添加如下信息
```sh
  # CentOS-Testing:
  # !!!! CAUTION !!!!
  # This repository is a proving grounds for packages on their way to CentOSPlus and CentOS Extras.
  # They may or may not replace core CentOS packages, and are not guaranteed to function properly.
  # These packages build and install, but are waiting for feedback from testers as to
  # functionality and stability. Packages in this repository will come and go during the
  # development period, so it should not be left enabled or used on production systems without due
  # consideration.
  [c5-testing]
  name=CentOS-5 Testing
  baseurl=http://dev.centos.org/centos/$releasever/testing/$basearch/
  enabled=1
  gpgcheck=1
  gpgkey=http://dev.centos.org/centos/RPM-GPG-KEY-CentOS-testing
  includepkgs=php*
```

3、完成后我们就可以使用这个源了，可以简单的 yum update ，也可以指定更新指定的软件
最后，确认一下系统中已经安装了PHP 5.2
```sh
  # rpm -qa |grep php

  php-cli-5.2.6-2.el5s2
  php-mbstring-5.2.6-2.el5s2
  php-devel-5.2.6-2.el5s2
  php-pdo-5.2.6-2.el5s2
  php-gd-5.2.6-2.el5s2
  php-dba-5.2.6-2.el5s2
  php-common-5.2.6-2.el5s2
  php-bcmath-5.2.6-2.el5s2
  php-xml-5.2.6-2.el5s2
  php-pear-1.5.1-2.el5s2
  php-ldap-5.2.6-2.el5s2
  php-5.2.6-2.el5s2
  php-mysql-5.2.6-2.el5s2


 # php -v

  PHP 5.2.6 (cli) (built: Sep 15 2008 20:42:05)
  Copyright (c) 1997-2008 The PHP Group
  Zend Engine v2.2.0, Copyright (c) 1998-2008 Zend Technologies
```

参考资料：
1、[pcre-6.6.2.el5_1.7.src.rpm](http://rpm.pbone.net/index.php3/stat/4/idpl/8077841/com/pcre-devel-6.6-2.el5_1.7.i386.rpm.html)
2、[Build PCRE RPM with Unicode Support](http://chrisjean.com/2009/01/31/unicode-support-on-centos-52-with-php-and-pcre/)
3、[How to patch and rebuild RPM package](http://bradthemad.org/tech/notes/patching_rpms.php)
4、[How To PHP5.1-PHP5.2](http://wiki.centos.org/HowTos/PHP_5.1_To_5.2)












