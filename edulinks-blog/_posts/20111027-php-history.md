---
title: PHP的历史 - 版本演变及区别
date: 2021-03-24 13:28:24
tags:
keywords: PHP历史, PHP版本, PHP
description: 介绍PHP的发展历史以及主要版本的特性。
---

1995年，Rasmus Lerdorf 开发了自己的个人主页。他用Perl编写了一系列脚本来方便自己的工作，随后，他将这系列脚本以“Personal Home Page Tools”的名称发布了出来，获得了非常好的反响。

1996年，由于脚本的良好反映，他们开发了一个C语言的实现（PHP 2.0）。一年之后，世界上的Web服务器，已经有1％开始使用PHP2.0。

1998年，随着World Wide Web的急速增长，PHP3.0的市场份额扩大到了10％。

2000年，推出PHP4以及Zend Engine。

2004年，PHP5推出，带来了一个全新的对象模型。

PHP当前的版本是PHP 5.4，当前的稳定版本是 5.3.6，而上一个稳定版本是 5.2.17 （已经不被支持）

PHP当前的版本分支主要有三种：PHP5.1.x、PHP5.2.x、PHP5.3.x。

PHP5.3在某些方面更具优势，在稳定性上更胜一筹，增加了很多PHP5.2所不具有的功能，比如内置php-fpm、更完善的垃圾回收算法、命名空间的引入、sqlite3的支持等等，是部署项目值得考虑的版本。强烈推荐PHP5.3.3

从PHP5.2.6开始，PHP提供了Thread Safe和Non Thread Safe两种版本，那么两种版本有何区别？

先从字面意思上理解，Thread Safe是线程安全，执行时会进行线程（Thread）安全检查，以防止有新要求就启动新线程的CGI执行方式而耗尽系统资源。Non Thread Safe是非线程安全，在执行时不进行线程（Thread）安全检查。

再来看PHP的两种执行方式：ISAPI和FastCGI。

ISAPI执行方式是以DLL动态库的形式使用，可以在被用户请求后执行，在处理完一个用户请求后不会马上消失，所以需要进行线程安全检查，这样来提高程序的执行效率，所以如果是以ISAPI来执行PHP，建议选择Thread Safe版本；

而FastCGI执行方式是以单一线程来执行操作，所以不需要进行线程的安全检查，除去线程安全检查的防护反而可以提高执行效率，所以，如果是以FastCGI来执行PHP，建议选择Non Thread Safe版本。

对于apache服务器来说一般选择isapi方式(Thread Safe版本)，而对于nginx服务器则选择FastCGI方式。
