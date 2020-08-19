---
title: 为PHP编译imap扩展
date: 2011-06-01 17:49:01
tag: 
---

最近为项目增加了邮箱登陆的验证，本地测试OK，部署至服务器上提示 imap_open 为未定义函数，由此可知缺少了IMAP的PHP扩展。
解决办法，首先看官网的安装说明，原来需要提前安装 imap-open2007e 才行。从[ftp://ftp.cac.washington.edu/imap/](ftp://ftp.cac.washington.edu/imap/)的网站上下载了源代码，但是这个的安装却让我比较头疼，因为没有 configure 文件，也没有一个说明文档。参考了其他朋友的办法，使用了下面的方法：
> wget ftp://ftp.cac.washington.edu/mail/imap.tar.Z
tar zxf imap.tar.Z
cd imap-2007a
make lr5 PASSWDTYPE=std SSLTYPE=unix.nopwd IP6=4
echo "set disable-plaintext nil" > /etc/c-client.cf
mkdir /usr/local/imap-2007a
mkdir /usr/local/imap-2007a/include/
mkdir /usr/local/imap-2007a/lib/
chmod -R 077 /usr/local/imap-2007a
rm -rf /usr/local/imap-2007a/include/*
rm -rf /usr/local/imap-2007a/lib/*
rm -rf /usr/sbin/imapd
cp imapd/imapd /usr/sbin/
cp c-client/*.h /usr/local/imap-2007a/include/
cp c-client/*.c /usr/local/imap-2007a/lib/
cp c-client/c-client.a /usr/local/imap-2007a/lib/libc-client.a


他这个过程中 rm –rf 的过程，我觉得是防止这几个目录中有其他不必要的文件，所以创建了文件夹后，又做了一次删除操作。完成这个过程之后，系统的 imap-open 环境就准备好了，进行php扩展的安装。
我这里因为已经安装了PHP，而且我也不想重新编译整个环境，所以我只需要编译这个扩展就行。系统中的PHP为5.2.9版本，找到对应版本的源代码，进入 ./ext/extension/imap/文件夹下，执行下面的步骤：
> /usr/local/apache/bin/phpize
./configure --with-php-config=/usr/local/apache/bin/php-config --prefix=/usr/local/apache/ --with-imap=/usr/local/imap-2007e/ –with-imap-ssl
make
make install

完成后，在php的extension/no-debug-non-zts-xxx 下就能看到编译好的so文件，添加到 php.ini 中，重启服务器，就可以看到imap 模块启动了。


参考资料：
1、[PHP IMAP Installation](http://www.php.net/manual/en/imap.installation.php)
2、[终于搞定PHP中安装imap扩展解决办法](http://hi.baidu.com/chenshake/blog/item/8266d4eae70d4cd6d439c9f9.html)
3、[PHP with imap support](http://www.directadmin.com/forum/showthread.php?p=131417)












