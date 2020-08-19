---
title: PHP中的PathInfo
date: 2009-09-17 17:55:01
tag: 
---

PHP中的全局变量$_SERVER['PATH_INFO']是一个很有用的参数，众多的CMS系统在美化自己的URL的时候，都用到了这个参数。

对于下面这个网址：

http://www.test.com/index.php/foo/bar.html?c=index&m=search

我们可以得到 $_SERVER['PATH_INFO'] ＝ ‘/foo/bar.html’,而此时 $_SERVER['QUERY_STRING'] = 'c=index&m=search';

通常，我们最初开始PHP程序编写的时候，都会使用诸如： http://www.test.com/index.php?c=search&m=main 这样的URL，这种URL不仅看起来非常奇怪，而且对于搜索引擎也是非常不友好的。很多搜索引擎收录的时候，都会忽略Query String之后的内容，google虽然不会忽略Query String，但是对于其他不含Query String的页面，会给于比较高的PR值。

下面是一段解析PATH_INFO的非常简单的代码：

<?php

if(!isset($_SERVER['PATH_INFO'] ) ){
$pathinfo='default';
}else{
$pathinfo=explode('/',$_SERVER['PATH_INFO']);
}

if(is_array($pathinfo) AND!empty($pathinfo)  ){
$page=$pathinfo[1];
}else{
$page='a.php';
}

require"$page.php";

?>


参考资料：
1、[PHP Parse Pathinfo](http://blog.affien.com/archives/2004/12/12/parsing-_serverpath_info/)
2、[CPAN PathInfo](http://search.cpan.org/%7Esnowhare/CGI-PathInfo-1.03/lib/CGI/PathInfo.pod)











