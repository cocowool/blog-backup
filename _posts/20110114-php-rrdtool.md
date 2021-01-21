---
title: 利用PHP的Popen实现RRDTOOL作图的动态输出
date: 2011-01-14 10:48:01
tag: 
keywords: rrdtool, rrdtool作图, php rrdtool, php rrdtool作图
description: RRDTOOL本身语法比较繁琐
---

最近看了一下RRDTOOL如何作图，语法确实比较繁琐，但不是太难。命令行下执行的时候，每次都需要输出成为一个文件。我们希望做一个应用，能够动态生成图表，看了看Cacti的实现方法，核心的部分就是使用了popen这个函数，将命令的输出放到了管道中，然后循环读取所有的数据，完成后，再以图片的形式输出到客户端。这样用户就能够看到动态的图片而不需要进行物理的存储了，下面是我截取的代码段，供大家参考。

```php
error_reporting(E_ALL);
//作图命令，注意看 graph 后面加了一个连接符
$command = '/opt/rrdtool/bin/rrdtool graph - --start=-86400 --end=-300 --title=Test --height=400 --width=800 DEF:value1="/home/echo/workspace/misc/tianjin_dpool_web_21_traffic_in_22.rrd":traffic_in:AVERAGE AREA:value1#ff0000 2>&1';

//测试管道符
session_write_close();
//用管道的方式执行命令，并且接收错误输出
$handle = popen("$command 2>&1", 'r');
$read = '';
while (!feof($handle)) {
	$read .= fgets($handle, 4096);
}
pclose($handle);
echo $read;

// Set the proper headers to allow caching
$this->request->headers['Content-Type']   = File::mime_by_ext('png');
$this->request->headers['Content-Length'] = strlen($read);
$this->request->headers['Last-Modified']  = date('r', time());
```

参考资料：
1、[RRDTOOL教程](http://blogold.chinaunix.net/u/12066/)












