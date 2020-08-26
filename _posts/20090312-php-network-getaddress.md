---
title: php_network_getaddresses 函数 getaddrinfo failed
date: 2009-03-12 17:57:01
tag: 
---

这个错误一般发生在有远程请求时，典型的错误信息是：
Warning: fsockopen(): php_network_getaddresses: getaddrinfo failed:Name or service not known in /home/carstyling/public_html/newnuke/modules/WebMail/pop3.php on line 70
Warning: fsockopen(): unable to connect to mx.freenet.de:110 in /home/carstyling/public_html/newnuke/modules/WebMail/pop3.php on line 70

这个问题并不是PHP的问题，而是出在域名解析环节。
可以修改/etc/resolv.conf，更改自己的域名解析服务器
或者自己在/etc/hosts中设置需要链接的位置

完成后重新刷新页面，这个错误就不会发生了。














