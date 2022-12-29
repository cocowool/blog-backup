---
title: CentOS中设置系统级代理
date: 2012-07-06 22:09:01
tag: 
keywords: centos, centos linux, centos 代理
description: 在Centos中设置全局代理。
---


**YUM代理设置**

编辑/etc/yum.conf，在最后加入

```sh
# Proxy
proxy=http://username:password@proxy_ip:port/
```

也可以使用proxy_username和proxy_password来配置代理的用户名和密码

这样的配置完成后，所有的用户在使用yum时，都会使用代理，可以说是全局代理。

如果需要为单独的用户配置代理，可以在用户的~/.bash_profile中设置。

**Wget的代理设置**

编辑/etc/wgetrc，在最后加入
```
# Proxy
http_proxy=http://username:password@proxy_ip:port/
ftp_proxy=http://username:password@proxy_ip:port/
```

**系统全局代理**

如果需要为某个用户设置一个系统级的代理，可以在~/.bash_profile中设置：
```sh
http_proxy="http://username:password@proxy_ip:port"
export_http_proxy
```

上面的设置只对某个用户生效，如果要对所有系统用户生效，写在/etc/profile中就可以了。

参考资料：
1、[CentOS5.6下设置代理服务器](http://blog.csdn.net/tsangchoonhsia/article/details/6676834)
2、[Virtualbox双网卡虚拟机Linux NAT不能上网](http://www.cnblogs.com/tigertall/archive/2012/01/02/2310253.html)
3、[关于Centos YUM的代理设置](http://blog.chinaunix.net/space.php?uid=20255102&amp;do=blog&amp;id=1712939)