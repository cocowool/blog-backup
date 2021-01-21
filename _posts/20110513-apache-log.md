---
title: Apache日志中的处理时间
date: 2011-05-13 16:15:01
tag: 
keywords: apache, apache日志, apache 处理时间
description: apache日志中处理时间%T到底该如何使用。
---

Apache的日志有很多可以自己定义的项目，其中一个 %T 能够显示出服务器处理请求所用的时间。我就是对这个的定义发生了疑问，所以做了一些考证。

在Apache2的中文手册中，是这样定义 %T 这个变量的。

> %T   处理完请求所花时间，以秒为单位。

在Apache2的英文文档中，定义如下：
> %T   the time taken to server the request, in seconds.

由此可见，这个时间表示的是服务器处理这个请求的总时间。 而不是Apache服务器解析PHP脚本，并且输出脚本的时间。因此，我们可以看到同样的一个页面，网速比较慢的用户访问时间会长，而网速较快的用户访问，则时间比较短。

%T记录的是以秒为单位的时间，这对于我们来说是不太够的。因为很多情况下，我们需要保证我们网页的响应速度在1秒以内。从Apache 2.0 开始，提供了一个新的参数 %D。可以记录服务器处理请求的微秒时间（注意和%T的定义不同）。

我在服务器上做了一次测试，代码中嵌入了一个执行时间的检查判断，同时监视日志文件中产生的时间。结果为：页面监测脚本执行时间为10009206毫秒，而日志中记录的是10009838，两者时间并不一样，日志中记录的时间稍微长一些，包含了DNS查询等一系列的过程。

PS：由这个问题也可以衍生出一个如何测算客户端网速的问题。有这样一个办法，在Header中输出服务器的响应时间，用户收到后，判断收到的时间，这个时间差就是在服务器和客户端之间所消耗的时间。

参考资料：
1、[Apache 2手册](http://lamp.linux.gov.cn/Apache/ApacheMenu/mod/mod_log_config.html#formats)
2、[Apache logs: how log does it take to server a request?](http://www.ducea.com/2008/02/06/apache-logs-how-long-does-it-take-to-serve-a-request/)












