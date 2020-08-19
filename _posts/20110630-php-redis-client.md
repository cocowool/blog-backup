---
title: Redis的PHP客户端
date: 2011-06-30 15:55:01
tag: 
---

Redis支持的客户端种类非常多，包括C、Java、PHP、Python等，本文主要介绍PHP客户端的安装和使用。


Redis的客户端实际上担负了通过网络协议与Redis Server进行通信的过程，通信的过程必须遵循协议规范，让客户的调用更加符合特定语言的使用习惯。现有PHP客户端：rediska、phpredis、Predis、Redisent。这些可护短中，有纯PHP的实现方案，也有二进制版本的实现方案。


官方提供的PHP客户端列表如下：




**特性比较**





/*---------------------- Predis ----------------------------*/


**Predis**


Predis是一个灵活和特性完备（PHP>5.3）的支持Redis的PHP客户端。当前版本为0.6.3，默认不支持PHP5.2。
主要特性如下：
完整的支持从1.2到2.4的Redis，并且支持当前正在开发的版本；
提供客户端实现的一致性哈希算法，支持自定义；
在单个或聚合连接中支持命令管道；（Command pipelining on single and aggregated connections）
能够通过TCP/IP或者Unix domain sockets连接到redis，支持持久连接；
自动连接Redis实例，使用“懒惰”方式，只在第一个命令发出时执行连接；
可以灵活定义客户端的命令集合；


Predis安装


/*----------------------- phpredis ---------------------------*/


**phpredis**


这是一个二进制版本的PHP客户端，按照的说法，效率要比Predis高。这个版本支持作为Session的Handler。这个扩展的有点在于无需加载任何外部文件，使用比较方便。缺点在于难于扩展，一般的PHP程序员无法对其做出扩展。考虑到Redis正在飞速发展过程中，缺乏扩展的特性还是有些影响的，需要维护过程中注意进行升级更新。


Max下整合XAMPP的安装（需要XAMPP的Dev环境）


sudo /Applications/XAMPP/xamppfiles/bin/phpize
sudo MACOSX_DEPLOYMENT_TARGET=10.6 CFLAGS="-arch i386 -arch x86_64 -g -Os -pipe -no-cpp-precomp" CCFLAGS="-arch i386 -arch x86_64 -g -Os -pipe" CXXFLAGS="-arch i386 -arch x86_64 -g -Os -pipe" LDFLAGS="-arch i386 -arch x86_64 -bind_at_load" ./configure --with-apxs=/Applications/XAMPP/xamppfiles/bin/apxs --with-php-config=/Applications/XAMPP/xamppfiles/bin/php-config
sudo make
sudo make install


然后修改php.ini，重启服务就可以了。


示例代码


<?php
echo 'phpredis sample:<br />';


error_reporting(E_ALL);
ini_set('display_errors','ON');


$redis = new Redis();
$redis->connect('127.0.0.1',6379);
$redis->set('first_key_phpredis', 'Hello world');


);


/*------------------- Rediska -------------------------------*/


**Rediska**


rediska 目前还处于 beta 阶段。他的代码托管在github上，可以方便的获取。另外也提供了PEAR版本，所以获取和安装都非常方便。可以使用PEAR包安装。


本文使用的是 Mac 下的 XAMPP提供的PEAR进行安装。首先添加频道地址：


localhost:bin shiqiang$ sudo ./pear channel-discover[pear.geometria-lab.net](http://pear.geometria-lab.net/)
Password:
Adding Channel "[pear.geometria-lab.net](http://pear.geometria-lab.net/)" succeeded
Discovery of channel "[pear.geometria-lab.net](http://pear.geometria-lab.net/)" succeeded


然后进行安装
localhost:bin shiqiang$ sudo ./pear channel-discover[pear.geometria-lab.net](http://pear.geometria-lab.net/)
Password:
Adding Channel "[pear.geometria-lab.net](http://pear.geometria-lab.net/)" succeeded
Discovery of channel "[pear.geometria-lab.net](http://pear.geometria-lab.net/)" succeeded
localhost:bin shiqiang$ sudo ./pear install geometria-lab/Rediska-beta
Unknown remote channel:[pear.zfcampus.org](http://pear.zfcampus.org/)
Did not download optional dependencies: channel://pear.[zfcampus.org/zf](http://zfcampus.org/zf), use --alldeps to download automatically
geometria-lab/Rediska can optionally use package "channel://pear.[zfcampus.org/zf](http://zfcampus.org/zf)" (recommended version 1)
downloading Rediska-0.5.6.tgz ...
Starting to download Rediska-0.5.6.tgz (116,203 bytes)
.........................done: 116,203 bytes
install ok: channel://pear.[geometria-lab.net/Rediska-0.5.6](http://geometria-lab.net/Rediska-0.5.6)


安装完成后，就可以看到本地的目录环境下已经有了Rediska的PEAR包
localhost:XAMPP shiqiang$ sudo find . -name 'Redis*'
./xamppfiles/lib/php/pear/data/Rediska
./xamppfiles/lib/php/pear/doc/Rediska
./xamppfiles/lib/php/pear/Rediska
./xamppfiles/lib/php/pear/Rediska/Options/RediskaInstance.php
./xamppfiles/lib/php/pear/Rediska/Zend/Application/Resource/Rediska.php
./xamppfiles/lib/php/pear/Rediska/Zend/Auth/Adapter/Redis.php
./xamppfiles/lib/php/pear/Rediska/Zend/Cache/Backend/Redis.php
./xamppfiles/lib/php/pear/Rediska/Zend/Log/Writer/Redis.php
./xamppfiles/lib/php/pear/Rediska/Zend/Queue/Adapter/Redis.php
./xamppfiles/lib/php/pear/Rediska/Zend/Session/SaveHandler/Redis.php
./xamppfiles/lib/php/pear/Rediska.php
./xamppfiles/lib/php/pear/test/Rediska
./xamppfiles/lib/php/pear/test/Rediska/tests/library/Rediska
./xamppfiles/lib/php/pear/test/Rediska/tests/library/Rediska/Options/RediskaInstanceTest.php
./xamppfiles/lib/php/pear/test/Rediska/tests/library/RediskaTest.php
./xamppfiles/temp/pear/download/Rediska-0.5.6
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Options/RediskaInstance.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Application/Resource/Rediska.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Auth/Adapter/Redis.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Cache/Backend/Redis.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Log/Writer/Redis.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Queue/Adapter/Redis.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska/Zend/Session/SaveHandler/Redis.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/library/Rediska.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/tests/library/Rediska
./xamppfiles/temp/pear/download/Rediska-0.5.6/tests/library/Rediska/Options/RediskaInstanceTest.php
./xamppfiles/temp/pear/download/Rediska-0.5.6/tests/library/RediskaTest.php
./xamppfiles/temp/pear/download/Rediska-0.5.6.tgz


**简单使用的示例**


<?php


error_reporting(E_ALL);
ini_set('display_errors','ON');


$options = array(
'namespace' => 'Application_',
'servers'   => array(
array('host' => '127.0.0.1', 'port' => 6379),
array('host' => '127.0.0.1', 'port' => 6380)
)
);


require_once 'Rediska.php';
$rediska = new Rediska($options);//创建Rediska对象，连接服务器


$key = new Rediska_Key('first_key');
$key->setValue('first_value');


$get_value = new Rediska_Key('first_key');
print $get_value;//取回存储的值


**相对于Memcache的区别**
1、支持的数据类型多样化。支持字符串、列表、集合、有序集合、Hash数组这几种数据类型。
2、可以灵活的指定key存储在哪台服务器上。


参考资料：
1、[http://rediska.geometria-lab.net/](http://rediska.geometria-lab.net/)
2、[Reids Which PHP Module to use](http://stackoverflow.com/questions/2001075/redis-which-php-module-to-use)
3、[PHP Redis](http://code.google.com/p/phpredis/)
4、[膜拜Redis](http://hi.baidu.com/thinkinginlamp/blog/item/3358c93d174e35ce9f3d62bf.html)
5、[redis客户端 predis与phpredis 比较](http://blog.csdn.net/lxzo123/archive/2011/05/12/6414458.aspx)













