---
title: PHP中如何处理时区
date: 2010-02-06 15:48:01
tag: 
keywords: php, 时区, timezone
description: 默认安装的LAMP环境中，时区默认设置在GMT时间，所以一般得到的时间都会比我们电脑中显示的时间早八个小时（假设你的时间设置正确，并且时区是在东八区）。
---

默认安装的LAMP环境中，时区默认设置在GMT时间，所以一般得到的时间都会比我们电脑中显示的时间早八个小时（假设你的时间设置正确，并且时区是在东八区）。

PHP提供了两个函数用来处理时区的操作:

* date_default_timezone_set()
* date_default_timezone_get()

如果我们希望更改服务器默认的时区，修改 php.ini 文件中的 ;date.timezone = ，添加自己需要的时区，例如 date.timezone = Asia/Chongqing，然后重启服务器就可以了。PHP支持的 timezone 的字符串在参考资料[[1](http://www.php.net/manual/en/timezones.php)]中可以看到。

用下面这一段代码，可以比较清楚的看到时区变化的影响：

```php
$now = time();
echo "The GMT now is : " . date('Y-m-d H:i:s', now()) . "<br />";
echo "The local time is : " . date('Y-m-d H:i:s', gmt_to_local($now) ) . "<br />";
date_default_timezone_set('Asia/Chongqing');
echo 'Set the timezone to :' . date_default_timezone_get() . "<br />";
echo "The GMT now is : " . date('Y-m-d H:i:s', now()) . "<br />" ;
echo "The local time is : " . date('Y-m-d H:i:s', gmt_to_local($now) ) . "<br />";
```

如果我们的站点是面向单一时区的用户，我想采用这个时区的时间来记载没什么问题，或者网站应用的类型并不 Care 时间的差别，那么我们用什么时间做存储都没关系。一旦我们的用户对于时区的影响有较大关系的时候，我想应该统一使用GMT的标准时间来进行存储，在显示的时候再根据用户所在的时区进行显示，这样虽然前端显示每次都需要去计算用户当前的时间，但是对于后台逻辑的统一性应该是大有裨益的。

目前，我们国家已经废除了[夏令时](http://en.wikipedia.org/wiki/Daylight_saving_time)的使用，但是世界上仍有其他一些国家在使用[夏令时](http://en.wikipedia.org/wiki/Daylight_saving_time)，我们在程序处理的时候应该注意这个问题，具体的资料可以参考[Daylight Saving](http://en.wikipedia.org/wiki/Daylight_saving_time)。

参考资料：
1、[List of Supported Timezones](http://www.php.net/manual/en/timezones.php)
2、[Daylight Saving](http://en.wikipedia.org/wiki/Daylight_saving_time)












