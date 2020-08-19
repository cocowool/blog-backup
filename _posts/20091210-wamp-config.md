---
title: WAMP环境配置
date: 2009-12-10 08:30:01
tag: 
---

选择的版本分别为
apache 2.2.14 with ssl
php 5.2.11
mysql 5.1.41
系统环境为 Windows XP SP3
安装过程：
1、安装apache。
> 这个非常简单，双击后一路 next ，在填写域名和主机名的时候可以随便写，比如 test.com/www.test.com ，然后写下自己的邮箱，安装完成。
完成后，通过[http://localhost](http://localhost)能够访问，并且看到 It works! 表示已经安装成功。
根据PHP官方的提示：
We do not recommend using a threaded MPM in production with Apache2.  Use the prefork MPM instead, or use Apache1. For information on why,   read the related FAQ entry on using Apache2 with a threaded MPM
对于默认的MPM模式官方并不支持，我们应当修改为 Prefork 模式，这个之后再讨论如何优化。

2、下面安装PHP。
> PHP在Windows下的安装有两种方式，一种是CGI、一种是apache的DLL模块。不管哪一种，我们都需要修改 apache 的配置文件去支持PHP。
我这里采用模块方式安装，在 httpd.conf 中添加：
# For PHP 5 do something like this:
LoadModule php5_module "E:/php/php5apache2_2.dll"
AddType application/x-httpd-php .php
# configure the path to php.ini
PHPIniDir "E:/php"
重启apache，编写 phpinfo.php ，则通过[http://localhost/phpinfo.php](http://localhost/phpinfo.php)可以查看到 php 已经安装完成。


3、安装 PHP 扩展。
> 安装PHP的扩展只需要将 ;extension=*.dll 前的注释去掉，重启服务即可。我打开了 curl、gd2、mbstring、mcrypt、mysql、mysqli、pdo、pdo_mysql、pdo_oci、pdo_oci8、pdo_sqlite、soap、xmlrpc。需要注意的是，有些扩展需要将特定的 dll 拷贝到系统目录下才能正常使用。
php_curl.dll CURL, Client URL library functions Requires: ibeay32.dll,ssleay32.dll (bundled)
php_mcrypt.dll Mcrypt Encryption functions Requires: ibmcrypt.dll
php_mysql.dll MySQL functions PHP >= 5.0.0, requires libmysql.dll   (bundled)
php_mysqli.dll MySQLi functions PHP >= 5.0.0, requires libmysql.dll   (libmysqli.dll in PHP <= 5.0.2) (bundled)
php_xmlrpc.dll XML-RPC functions PHP >= 4.2.1 requires: iconv.dll(bundled)

> 同时应该注意将 extension_dir 设置为系统对应的值，完成后重启 apache 即可。

4、安装 Mysql。
> Mysql 的安装也比较简单，从官方网站下载了安装文件后，双击一路运行到底。结束的时候可以选择是否到 Mysql 网站注册 和 马上进行服务器的配置。我们可以去掉服务器注册那一步，进而继续服务器的配置。
服务器的配置最主要就是选择 InnoDB 的存储位置和数据库的用户名密码，到最后，看全部执行完毕后安装就完成了。

> 

5、配置虚拟机
> 项目比较多的时候，使用 apache 的虚拟机还是非常方便的。
首先打开 httpd.conf 中关于 vhost 配置文件的引用，这样我们所有的虚拟机配置文件都写在 extra/httpd-vhosts 中，方便管理。
# Virtual hosts
Include conf/extra/httpd-vhosts.conf
然后在 httpd-vhost 文件中加入
<VirtualHost *:80>
ServerAdmin cocowool@gmail.com
DocumentRoot "D:/workspaces/DefaultWorkspace/heep/"
ServerName www.heep.com
ErrorLog "logs/heep.com-error.log"
CustomLog "logs/heep.com-access.log" common
<Directory "D:/workspaces/DefaultWorkspace/heep/">
AllowOverride Options
Allow from All
</Directory>
</VirtualHost>

6、安装 Zend Optimizer
> Optimizer 可以到[Zend 的官方网站](http://www.zend.com)下载。需要首先注册一个账号，这点没有 Mysql 好，不过人家毕竟没说这事开源的。虽然不开源 ZendOptimizer 确是免费的。下载完成后 一步一步安装就可以了，期间会提示选择正确的 PHP 和 Apache 的位置。

经过以上的步骤，一个 WAMP 的开发环境就配置好了，基本上能够满足开发一般应用的需要。

参考资料：
1、[apache 2 MPM 的选择与配置](http://jdlog.spaces.live.com/blog/cns!FBEA55C365DE0B74!249.entry)
2、[windows中apache2配置性能优化以及测试小结](http://www.asgone.net/windows-apache2-cognos-cube-and-test/)
Technorati 标签:[WAMP](http://technorati.com/tags/WAMP),[PHP](http://technorati.com/tags/PHP)












