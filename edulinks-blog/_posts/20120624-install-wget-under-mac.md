---
title: Mac Lion安装wget工具
date: 2012-06-24 22:48:01
tag: 
keywords: wget, mac wget, mac安装wget
description: Mac下如何俺去昂wget，Wget是一个支持HTTP、FTP的下载工具。
---

上次我们提到了[在Mac下安装gcc](http://xsite.sinaapp.com/?p=7)，今天来安装一个经常在命令行下使用的工具Wget。

Wget是一个支持HTTP、FTP的下载工具，经过合理的配置参数，我们甚至可以将一个网站的内容全部拷贝下来。
笔者的系统是Mac OS Lion 10.7.4 ，安装好了gcc，如果没有安装gcc的同学，需要下载Xcode，然后通过下载命令行工具来进行安装。

下载Wget的源代码：[下载地址](http://ftp.gnu.org/gnu/wget/wget-1.13.4.tar.gz)

直接安装的话，会发生下面的错误。

```sh
checking for compress in -lz... yes
checking for gpg_err_init in -lgpg-error... no
checking for gcry_control in -lgcrypt... no
checking for libgnutls... noconfigure: error: --with-ssl was given, but GNUTLS is notavailable.
username@host:~/wget-1.13.4 $
```

在[zioer的站中也给出了一个解决方法](http://www.zioer.com/mac-os-%E5%AE%89%E8%A3%85-wget/)，但这里我找到了一个更好的办法。

```sh
#  tar zxvf wget-1.13.4.tar.gz
# cd wget-1.13.4
wget-1.13.4 root# ./configure  --with-ssl=openssl
wget-1.13.4 root# make
wget-1.13.4 root# make install
```

注意，你如果不是使用root用户，那么在安装的这一步需要使用sudo授权。

参考资料：
1、[MAC OS安装Wget](http://www.zioer.com/mac-os-%E5%AE%89%E8%A3%85-wget/)
2、[Install Wget On OS X Lion](http://thomashunter.name/blog/install-wget-on-os-x-lion/)
3、[Discussion about the Nettle crypto library ()](http://comments.gmane.org/gmane.comp.encryption.nettle.bugs/206)