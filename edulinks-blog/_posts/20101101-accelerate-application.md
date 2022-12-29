---
title: 加速你的网络应用
date: 2010-11-01 20:06:01
tag: 
keywords: 网页加速, 网页优化, 页面加速
description: 本文介绍提高网页响应速度的方法。
---

平时我们希望提高应用的响应速度时，常用的有以下一些方法：
> 使用Gzip
减少Http Request次数
增加过期头信息 Expire Header
压缩CSS和Javascript文件

更多的方法，我们可以参考[Yahoo的34条前端优化规则](http://developer.yahoo.com/performance/rules.html)以及[Google的前端性能最佳实践](http://code.google.com/speed/page-speed/docs/payload.html#GzipCompression)，这两个主题都包含了大量的前端优化的内容。本篇着眼于如何结合Codeigniter的使用，来加速前端的性能。

1、第一条是使用Gzip。

在Codeigniter中，我们可以很方便的在 application/config/config.php 中打开 $config[‘compress_output’] = TRUE 这个选项，这样通过 view 输出的内容自动的就会进行gzip压缩。我对比了一个原来为24kb的首页，压缩后只有6.5k，可以说效果非常明显。

但是Codeigniter的gzip压缩选项打开后，view中输出时就不能再有 echo 语句，否则会发生如下的错误：

> Fatal error: ob_start() [ref.outcontrol]: Cannot use output buffering in output buffering display handlers in

我这里的解决办法是使用 ini_set 设置全局的PHP变量，默认打开文件压缩。因为懒得改代码中的 echo 了，如果比较容易修改，可以改为使用 set_output 进行替换，也不会有问题。

**扩展：**

打开这个选项后，实际上我们只能压缩PHP脚本输出的部分，而现在网站中的CSS和JS文件也不小，所以对这一部分进行压缩也是比较必要的。这首先需要我们的服务器支持 gzip 压缩，这里我只是考虑 apache 2.x 的情况，apache 1.3 和 IIS 的情况，可以 Google 之。

下面这个方法是在 Linux 和 Apache 的环境下实验通过，需要有修改 php.ini 和 .htaccess 文件的权限。
a、首先检查服务器是否支持GZIP。使用phpinfo()打印服务器信息，查看**_ENV["HTTP_ACCEPT_ENCODING"]**and**HTTP_ACCEPT_ENCODING**看是否包含 gzip。
b、确保 apache 的 mod_deflate 模块已经激活。
c、在项目的 .htaccess 文件中加入下面的语句，apache 服务器就会自动帮你压缩你希望进行压缩的文件

> AddOutputFilterByType DEFLATE text/plain
AddOutputFilterByType DEFLATE text/html
AddOutputFilterByType DEFLATE text/xml
AddOutputFilterByType DEFLATE text/css
AddOutputFilterByType DEFLATE text/javascript
AddOutputFilterByType DEFLATE application/xhtml+xml
AddOutputFilterByType DEFLATE application/x-javascript
AddOutputFilterByType DEFLATE application/javascript

在 apache 1.3 中，可以使用 mod_gzip 进行压缩，具体的设置方式就不赘述了。另外，试验中发现，如果 apache 服务器以 FastCGI 的方式来解析 PHP，上面这种设置会失效。具体的原因和解决方法还没找到相应的资料。

我们的一个项目中，使用了HostMonster的虚拟主机，Google搜到了一些文档，据说可以在HostMonster的虚拟主机上配置打开Gzip的选项，多次试验也没成功，后来咨询了HostMonster的工作人员，确认了虽然服务器上有mod_deflate模块，但并不是给消费者使用的，我那个气愤呀。

2、减少Http的请求次数

用户的带宽是非常有限的，如果你的网站上有众多的小文件，则每个文件请求建立连接、下载这个三次握手的过程是非常消耗用户的带宽资源的，所以可以使用一些技术来将多个文件合并为一个文件一次下载。
可以使用 Css Sprite 的技术，将页面中需要的小图标 icon 类集中在一个文件中。因为每个图标的大小一般都很小，大概都不超过 2-3 kb ，整合成一个文件仍能够将文件大小控制在合理的范围之内。

3、减少JS和CSS文件的尺寸

随着网络应用的丰富，现在网页中的引用的JS和CSS文件越来越多，也越来越大。我们平时在编程的时候，为了浏览的美观与方便，会对代码进行格式化，增加注释和空行，使用有意义的变量名等。实际上对于浏览器来说，解析和执行这些文件是不要保留这些格式的，因此对JS和CSS文件进行一些压缩的处理就可以达到减少文件尺寸的目的。

4、设置过期的头信息

在Codeigniter中，可以通过调用 $this->output->set_header(); 来设置头信息。从而控制浏览器如何决定来处理已经被缓存的内容。

参考资料：
1、[Speed UP your web application by Codeigniter](http://saidur.wordpress.com/2008/05/03/speed-up-your-web-application-by-codeigniter/)
2、[HTTP输出Gzip的注意事项](http://blog.belltoy.net/notes-of-gzip-compression.html)
3、[Yahoo的34条前端优化规则](http://developer.yahoo.com/performance/rules.html)
4、[Google的前端性能最佳实践](http://code.google.com/speed/page-speed/docs/payload.html#GzipCompression)
5、[Cannot use output buffering in output buffering display handlers](http://codeigniter.com/forums/viewthread/84317/)
6、[Force GZIP compress on your HostMonster hosted website](http://falcon1986.wordpress.com/2009/01/29/forcing-gzip-compression-on-your-hostmonster-hosted-website/)












