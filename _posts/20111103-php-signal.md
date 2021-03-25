---
title: php的信号(signal)处理
date: 2021-03-25 20:29:41
tags:
---

信号（signal）是unix/linux世界里被使用得最多的技术之一，它被用来做一些很基本的进程通讯。大多数情况下，我们是用来终止一个进程。

我想大家都用过kill吧，这个就是unix/linux系统里被用得最多的一个信号发送工具，它默认发出SIGTERM的信号。TERM，就是terminal（终止）的意思。

而使用ctrl+c终止一个进程的时候，一个SIGINT的信号会被发送给你所终止的程序。

另外一个大家可能用得比较多的就是HUP信号了。很多程序都会捕捉这个信号，重载配置。例如nginx就允许你kill -HUP pid来对nginx.conf进行重载。

更多的信号，可以通过man signal或者man 3 signal或者阅读signal.h来获得。

不过其实我这篇文章的本意并不是介绍signal，而是介绍PHP下如何实现signal。

php的signal功能是使用pcntl_*系列函数来实现的。而为了保证效率，信号的捕捉是通过declare ticks来实现。很变态吧。由于declare ticks不能影响父文件，所以这个declare必须在最父级的文件里启用，这导致我们无法完美地封装某些功能块。

不过，我的本意也不是要讨论它的不完美，我只是想介绍一下PHP下的signal实现而已，似乎到现在我都没进入正题。而实际上，正题将会很少。基本上，我只想贴一个例子。如下：

```php
function sighandler($signo) {
	echo "haha\n";
}

declare(ticks=1);

pcntl_signal(SIGINT, "sighandler");

sleep(30);
```

运行过程如下：
1）开始
2）给SIGINT信号注册个回调函数sighandler
3）sleep
4）ctrl+c，发送SIGINT信号
5）sighandler输出haha