---
title: PHP中跨时区应用的解决方法
date: 2010-07-17 12:17:01
tag: 
keywords: 时区, 跨时区, PHP处理时区
description: 不同时区登录的用户需要看到自己时区的时间，同时也要能够进行时区的切换的PHP方案。
---

现在有一个跨时区的应用，不同时区登录的用户需要看到自己时区的时间，同时也要能够进行时区的切换，有什么好办法么。我的思路是，系统中所有存储的时间都是GMT（UTC）时间，用户登录时，根据用户所在的时区进行对应的显示。

首先了解一下PHP中时区的设置方法。PHP中进行设置的方法比较灵活多样，可以在php.ini中设置date.timezone属性、可以通过代码，调用ini_set(‘date.timezone’, ‘’)设置，也可以使用函数 date_default_timezone_set(),或者在htaccess文件中设置。

服务器的默认时区，如果设置的和我们希望的时区不符，而且我们也没有权限修改全局的时区配置，就只有借助于代码了。

PHP还提供了一个方便的函数，gmdate()，可以让我们不用关心服务器的时区设置而始终获得GMT时间，我的思路就是基于这个函数。

我的项目中使用了Codeigniter这个框架，框架中的date这个helper提供了几个方便的函数，可以用来处理应用中的多时区情况。

其中 now() 始终返回的是gmt的当前时间；
local_to_gmt() 可以将本地的时间转换为gmt时间；
gmt_to_local() 可以将gmt时间转换为本地时间；

考虑一个典型的应用场景：
**用户登陆后，要显示当前时间**。这是我们可以使用now()获得标准的gmt时间，然后使用gmt_to_local()函数转化为用户所在时区的时间。

**用户要发布一个时间**。用户发布了一个“2010-07-10 18:30:00”的时间，我们不能直接存入数据库，必须先利用local_to_gmt() 转化标准的gmt时间存入数据库，这样才能保证整个系统中的时间保持一致。

这两个函数的细节，其实都是根据时区，然后进行相应的运算得来。计算的时候，也可以考虑夏令时，但是所在时区夏令时的开始和结束时间，则需要自己维护。

codeigniter中提供了一份较为完整的时区列表，timezone_menu() 可以显示一个时区的下拉列表，但是这个列表中的时间不能完全对应到PHP自带的时区显示上，这是PHP本身的问题，不过可以通过下面这个函数，来让输入的每个时区，都可以获得一个对应的时区文字显示。

```php
if( ! function_exists("tz_offset_to_name") ) 
{ 
    /* Takes a GMT offset (in hours) and returns a timezone name */ 
    function tz_offset_to_name($offset) 
    { 
            $offset *= 3600; // convert hour offset to seconds 
            $abbrarray = timezone_abbreviations_list(); 
            foreach ($abbrarray as $abbr) 
            { 
                    foreach ($abbr as $city) 
                    { 
                            if ($city['offset'] == $offset) 
                            { 
                                    return $city['timezone_id']; 
                            } 
                    } 
            } 
            return FALSE; 
    } 
}
```



参考资料：
1、[PHP时区设置方法](http://www.111cn.net/phper/apache/33705.htm)












