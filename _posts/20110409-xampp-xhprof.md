---
title: 为Lampp环境配置xhprof
date: 2011-04-09 15:27:01
tag: 
keywords: xphrof, lampp, linux
description: XAMPP中没有提供xhprof的支持，本文提供独立编译安装的步骤。
---

为了方便，一般在本地的测试环境中我都使用了 XAMPP 或者 LAMPP 。（[Apache Friends XAMPP](http://www.apachefriends.org/en/xampp.html)）但是默认的XAMPP中没有提供xhprof的支持，为此需要自己独立编译。下面记录了编译的过程。

1、如果你的XAMPP没有 DEVEL包，那么编译是通不过的。我就是因为编译时遇到 include php.h 时就失败了。所以需要先确保下载并安装了 xampp 的 devel packages。下载地址：[xampp-linux-devel](http://www.apachefriends.org/download.php?xampp-linux-devel-1.7.4.tar.gz)，然后解压到安装的目录  tar -xvzf file -C /opt。下载 xampp 的 devel 包的时候，注意要选择和自己当前的版本一致的 devel 包，因为 php 的扩展编译的时候，会附加版本信息，启动时进行检查，如果不一致，即便能够编译成功，也是不能够使用的。
完成后，你会发现lampp的目录下多了个include目录，里面是一些必要的头文件。

2、下载 xhprof 的[源代码](http://pecl.php.net/get/xhprof-0.9.2.tgz)。 解压缩，然后进入 xhprof/extension/ 目录

3、依次执行以下命令：

```sh
/opt/lampp/bin/phpize
./configure --with-php-config=/opt/lampp/bin/php-config --prefix=/opt/lampp/
make
make test
make install
```

之后就会看到安装完成的提示。之后去 /opt/lampp/lib/php/extensions/no-debug-on-zts-20060613/ 下查看，会看到已经有 xhprof.so 了。

4、 在 /opt/lampp/etc/php.ini 中添加 extension="xhprof.so" 重启lampp。

5、 重启服务器后，就可以看到phpinfo中xhprof的信息了。

PS：我在使用lampp 1.7 的时候，编译得到的 xhprof.so 不能够够正常加载，提示版本号不对，可能是


参考资料：
1、[Apache Friends XAMPP](http://www.apachefriends.org/en/xampp.html)
2、[XAMPP Devel Packages](http://www.apachefriends.org/download.php?xampp-linux-devel-1.7.4.tar.gz)