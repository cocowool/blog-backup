---
title: 防止Memcached的DDOS攻击另外一个思路
date: 2018-03-28 19:09
tag: 
category: 运维
---

3月3日，国家互联网应急中心通报了一条消息[关于利用memcached服务器实施反射DDoS攻击的情况通报](http://www.cert.org.cn/publish/main/9/2018/20180303110550151219900/20180303110550151219900_.html)通告了 memcached 服务器漏洞被黑客利用的情况，笔者的一台服务器也存在漏洞，因此将漏洞封堵的方法与大家分享一下。

### 攻击的原理

memcached反射攻击利用了在互联网上暴露的大批量memcached服务器（一种分布式缓存系统）存在的认证和设计缺陷，攻击者通过向memcached服务器IP地址的默认端口11211发送伪造受害者IP地址的特定指令UDP数据包（stats、set/get指令），使memcached服务器向受害者IP地址反射返回比原始数据包大数倍的数据（理论最高可达5万倍，通过持续跟踪观察攻击流量平均放大倍数在100倍左右），从而进行反射攻击。
本来Memcached提供的缓存服务应该只是对内部访问的，因此就设置了所有地址可访问，安全限制上就比较弱，黑客就利用这个特点，专门在互联网上找开放端口的 Memcached...

> The web page caching utility was never designed to be internet-accessible and requires no authentication to access. But some administrators - as well as some Linux instances by default - have left TCP or UDP port 11211 open to internet-borne requests.


### 处置办法
互联网应急中心给出了3条建议：
> 1、在memcached服务器或者其上联的网络设备上配置防火墙策略，仅允许授权的业务IP地址访问memcached服务器，拦截非法的非法访问。
2、更改memcached服务的监听端口为11211之外的其他大端口，避免针对默认端口的恶意利用。
3、升级到最新的memcached软件版本，配置启用SASL认证等权限控制策略（在编译安装memcached程序时添加-enable-sasl选项，并且在启动memcached服务程序时添加-S参数，启用SASL认证机制以提升memcached的安全性）。

我分析了一下，1 需要单位从整体的网络策略上进行调整，但是系统管理员负责的这部分职责还是要继续落实；2 需要修改端口，势必设计应用端的修改，我觉得算不上是上策；3 需要升级到最新版本，升级后应用是否会有问题还需要测试验证。

经过仔细分析后，发现 memcached 支持一个```-l```参数，允许指定监听的地址，默认是```0.0.0.0```也就是允许所有的地址链接。我们可以通过修改```-l```参数，绑定本地地址，这样就可以避免被外部攻击了。但是这里有个问题，一般缓存服务器是供前端或其它服务器公共调用的，一般不是本机使用，而且外部使用的设备往往多于1台。在早期的 Memcached 版本(我的环境是1.4.4)中```-l```参数不支持绑定多个地址。在最新的1.5.6版本中，已经能够支持绑定多个地址，具体形式如下：

```bash
$./memcached -l 127.0.0.1:11211,192.168.0.20:11211 -vv -u memcached
```
这样，就可以避免修改应用，又达到了防治漏洞的目的。

参考资料：
1、[国家互联网应急中心](http://www.cert.org.cn)
2、[关于利用memcached服务器实施反射DDoS攻击的情况通报](http://www.cert.org.cn/publish/main/9/2018/20180303110550151219900/20180303110550151219900_.html)
3、[CNCERT关于Memcached服务器反射攻击的情况通报 2](http://www.sohu.com/a/225346899_354899)












