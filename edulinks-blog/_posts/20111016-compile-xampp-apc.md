---
title: 在Lion上为XAMPP编译APC
date: 2011-10-16 11:22:01
tag: 
keywords: mac, mac xampp, xampp编译APC
description: 介绍在Mac下为XAMPP编译APC的方法。
---

平时在Mac下使用XAMPP进行开发，最近需要用到APC扩展，从PECL上下载了扩展的源文件，在本地进行了编译安装。下面是安装步骤。


1、首先从[http://pecl.php.net/package/APC](http://pecl.php.net/package/APC)下载源码包，目前的稳定版本是 3.1.9。
2、解压缩后进入到源码目录
3、sudo /Applications/XAMPP/xamppfiles/bin/phpize
4、sudo MACOSX_DEPLOYMENT_TARGET=10.7 CFLAGS="-arch i386 -arch x86_64 -g -Os -pipe -no-cpp-precomp" CCFLAGS="-arch i386 -arch x86_64 -g -Os -pipe" CXXFLAGS="-arch i386 -arch x86_64 -g -Os -pipe" LDFLAGS="-arch i386 -arch x86_64 -bind_at_load" ./configure --with-apxs=/Applications/XAMPP/xamppfiles/bin/apxs --with-php-config=/Applications/XAMPP/xamppfiles/bin/php-config
5、sudo make
6、sudo make install


完成后修改 php.ini 文件，添加如下一行


apc.shm_size = 32M


注意，如果不写M，则会出现下面的警告：

PHP Warning:  PHP Startup: apc.shm_size now uses M/G suffixes, please update your ini files in Unknown on line 0


在第四步中如果不添加一些编译参数，则会出现下面的错误：


PHP Warning:  PHP Startup: Unable to load dynamic library '/Applications/XAMPP/xamppfiles/lib/php/php-5.3.1/extensions/no-debug-non-zts-20090626/apc.so' - dlopen(/Applications/XAMPP/xamppfiles/lib/php/php-5.3.1/extensions/no-debug-non-zts-20090626/apc.so, 9): no suitable image found.  Did find:\n\t/Applications/XAMPP/xamppfiles/lib/php/php-5.3.1/extensions/no-debug-non-zts-20090626/apc.so: mach-o, but wrong architecture in Unknown on line 0


参考资料：
1、[Compile Apc for XAMPP on Snow Leopard](http://blog.elinkmedia.net.au/2010/05/04/compile-apc-for-xampp-on-snow-leopard/)
2、[http://pecl.php.net/package/APC](http://pecl.php.net/package/APC)
3、[http://snipt.net/mik/compile-php-extension-on-mac-pro-64-bit-os-x-leopard/](http://snipt.net/mik/compile-php-extension-on-mac-pro-64-bit-os-x-leopard/)












