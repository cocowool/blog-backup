---
title: 提高Mac下itunes的下载速度
date: 2012-07-28 08:57:01
tag: 
---

给老婆买了iPhone4s之后，开始在Mac上用iTunes下载软件，发现速度龟速，好歹家里也是10M光纤啊，这个小水管的下载速度绝对不能忍受，于是百度了一下，发现可能是域名解析造成的问题。苹果为App Store准备了a1.phobos.apple.com到a2000.phobos.apple.com一群服务器，如果连接到比较慢的服务器上，那肯定是小水管了。


解决方案就是探测一个最快的IP，然后在HOSTS文件中将所有的域名都指向这个IP。看网上的一些资料，都是使用一个Windows下的工具来探测，可是我这里是Mac没有Windows啊，想想这个问题不难，写了一个脚本来探测最快的IP。代码如下：


1#!/bin/sh
2
3#Findthe fastest apple store hosts
4
5printf "%-20s\t%-16s\t%-10s\n" "Domain" "Ip" "Avg Ping"
6#printf "Domain\tIp\tAvg Ping\n"
7for((I=1 ;I < 2001; I++ ));do
8HOST="a$I.phobos.apple.com"
9TEMP=$(ping -c 4 $HOST)
10IP=$(echo$TEMP | grep -oE '[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}' | uniq)
11TIME=$(echo$TEMP | grep -oE '\/([0-9]*\.[0-9]*)\/' | grep -oE '[0-9]*\.[0-9]*')
12printf "%-20s\t%-16s\t%-10s\n" $HOST $IP $TIME
13done




使用这种提速的方法有个问题需要注意，万一哪天你所指向的那个IP停机维护了，可能你都无法访问iTunes的App Store了，这时只需要找一个可用的IP，替换一下HOSTS中的内容就可以了。


使用这种方式后，下载的速度有所提高，但是并没有到达令人满意的程度。后来在参考资料[4](http://briian.com/?p=6667)中看到单独的ping值并不能反映网络的快慢，那么究竟如何找到一个最合适的下载服务器呢，这个还需要研究一下。


在测试过程中，我将DNS切换到了Google Public DNS上，发现同样的域名a1.phobos.apple.com返回的IP发生了变化，猜测Apple在iTunes App Store的部署上采用了CDN的方式。虽然用了8.8.8.8之后，ping的一些结果变快了，但是Mac的系统更新变的特别的慢，应该是DNS将IP解析到国外去造成的。


看来网络世界的配置还真是复杂，一个改动可能影响多个方面，要想达到一个最优，只能是动态调整了。


参考资料：
1、[加快iTunes从苹果App Store应用商店下载软件或App更新包速度的方法](http://www.mac52ipod.cn/post/speed-up-apple-itunes-download-speed-from-app-store-by-dns.php)
2、[提高iTunes下载速度终极大法](http://bbs.app111.com/thread-109285-1-1.html)
3、[修改HOST提高下载速度](http://bbs.pcbeta.com/forum.php?mod=viewthread&tid=912757)
4、[Google超快速DNS服务器：8.8.8.8和8.8.4.4](http://briian.com/?p=6667)
5、[Google Public DNS](https://developers.google.com/speed/public-dns/)














