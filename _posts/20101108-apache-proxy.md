---
title: 使用Apache的Proxy模块实现对被代理网站的访问
date: 2010-11-08 11:09:01
tag: 
---

最近有个需求，原本一个动态的站点，有些页面要静态化。但是静态化的文件又不希望和动态程序放在一起。并且URL也不希望发生变化，于是考虑使用Rewrite来实现。下面是一些使用的心得。
1、使用Rewrite规则来实现
Apache 的 Rewrite 是一个非常强大的工具，他的手册提供了丰富的实例可供我们学习，利用下面这个规则，可以将对www.a.com 这个网站的特定请求都定向到 www.b.com 服务器上。
ServerName www.a.com
RewriteEngine On
RewriteRule ^/a/(.*) http://www.b.com [P]
2、使用反向代理
ProxyRequests Off
ProxyPass       /a      http://house.baidu.com/bj
ProxyPassReverse /a     http://house.baidu.com/bj
ProxyPassReverseCookieDomain data.house.sina.com.cn l.house.sina.com.cn
ProxyPassReverseCookiePath / /a/
ProxyErrorOverride On


这两种方法都可以实现用 http://www.a.com/a/abc 的形式来访问实际上是 http://www.b.com/abc 的内容
但是，如果用户输入的URL在 www.b.com 中并不存在，www.b.com 发生转向时，会显示 www.b.com 的url
如果 www.b.com 上全是静态文件，文件无法访问到的时候，会导向404页面，默认导向被代理服务器的404。
但是使用ProxyErrorOverride On 命令后，可以使用代理服务器的404页面。


访问的目标文件中，对于JS和CSS的链接。如果采用了相对链接，则会默认去 www.a.com 上请求相应的文件。
使用代理模式时，页面中对于Cookie的操作可能会有问题。以l.house.x.com.cn代理到data.house.x.com.cn做了实验，
bj/search/0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-0-1.html 页面中的浏览历史出现了问题。
该指令允许你将一个远端服务器映射到本地服务器的URL空间中，此时本地服务器并不充当代理角色，而是充当远程服务器的一个镜像。
ProxyPass 支持构建一个连接池来提高服务器的服务性能。配置形式为在 URL 后面追加参数，如下：
ProxyPass /example http://backend.example.com smax=5 max=20 ttl=120 retry=300
其中
smax 表示Apache根据需要创建的连接数量。这些数量会在 ttl 设置到期前被 Apache 关闭。
max 表示 允许与被代理服务器链接的最大数量。默认的链接数量，是当前MPM中配置的最大链接数。在Prefork 模式中，这个数值永远是1.
在 Woker 模式中，连接数量由子线程来控制（ThreadsPerChild）。Apache创建的连接数量不会超过配置的数目。
ttl 表示以秒为单位，超过 smax 允许的非活动链接的存活时间。Apache会关闭超过 ttl 设置的非活动链接。
retry 表示连接池中为每次请求进行重试的最大时间限制，单位为秒。如果与被代理服务器之间的连接出现了问题，Apache在这个时间到期前，不会向客户端返回错误。
可以利用这个时间，对被代理服务器进行维护或重启等操作。
对于被代理服务器返回的重定向信息，可以使用 ProxyPassReverse 对其中的URL进行修改。只有明确指定的应答头会被重写，其它应答头保持不变，并且HTML页面中的URL也不会被修改。如果被代理的内容包含绝对URL引用，那么将会绕过代理。有一个第三方模块可以检查并改写HTML中的URL引用，该模块就是Nick Kew编写的[mod_proxy_html](http://apache.webthing.com/mod_proxy_html/)。
综合起来，要想实现这个目的，就必须使用 apache　的反向代理模块。


参考资料：
1、[Apache URL重写](http://lamp.linux.gov.cn/Apache/ApacheMenu/rewrite/index.html)
2、[Proxy Module](http://lamp.linux.gov.cn/Apache/ApacheMenu/mod/mod_proxy.html#access)












