---
title: Mac下遇到 'reading initial communication packet’ 问题
date: 2016-08-28 08:51:01
tag: 
keywords: mysql, lost connection to mysql
description: Lost connection to MySQL server 问题解决记录。
---

今天在开发过程中，一个单位跑的好好的项目，在家中的Mac下运行时，遇到了下面这个错误：

"Lost connection to MySQL server at 'reading initial communication packet, system error: 61"

看错误描述应该是无法连接数据库，OK，我先检查数据库是否正常工作，phpmyadmin看了一下一切正常。

看了一下数据库配置：hostname 使用的是 127.0.0.1，看了一下网上的解决方案，大多是因为防火墙或者网络访问不通造成的，那我这可是127.0.0.1本地地址，按理说不会不通呀，那我就换成 localhost 试一下，结果居然好了。

检查一下 my.cnf 并没有任何与地址解析相关的配置，应该使用的默认配置。

根据 Stack Overflow 中的讨论，推论出因为默认安装的mysql绑定的地址是 localhost 所以使用IP访问时mysql的访问策略拒绝了访问，解决方法是修改 my.cnf 或者允许IP地址访问。


参考资料：
1、[Stack Overflow](http://stackoverflow.com/questions/5755819/lost-connection-to-mysql-server-at-reading-initial-communication-packet-syste)
2、[51Jb](http://www.jb51.net/article/30774.htm)