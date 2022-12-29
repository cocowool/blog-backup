---
title: 与域名相关的几个命令
category: 运维
date: 2020-10-24 20:47:50
tags:
keywords: host, nslookup, dig, whois, 域名命令
description: 本文介绍与域名相关的几个命令，通过这几个命令，我们能够快速了解域名对应的IP地址、A记录、MX记录等各种与域名相关的信息，相信熟练使用这几个命令之后，在域名问题排查方面将会更加得心应手。
---


> 本文在 MacOS 10.15.6 环境下验证通过。

本文介绍与域名相关的几个命令，通过这几个命令，我们能够快速了解域名对应的IP地址、A记录、MX记录等各种与域名相关的信息，相信熟练使用这几个命令之后，在域名问题排查方面将会更加得心应手。

## host

> host is a simple utility for performing DNS lookups. It is normally used to convert names to IP addresses and vice versa. When no arguments or options are given, host prints a short summary of its command line arguments and options.

`host` 命令及主要参数如下图所示。

![image-20201021220730538](20201017-commands-to-domain/image-20201021220730538.png)

使用 `host` 命令查看下百度的域名

```sh
shiqiang@QKing ~ % host baidu.com
baidu.com has address 39.156.69.79
baidu.com has address 220.181.38.148
baidu.com mail is handled by 10 mx.maillb.baidu.com.
baidu.com mail is handled by 20 mx50.baidu.com.
baidu.com mail is handled by 20 jpmx.baidu.com.
baidu.com mail is handled by 20 mx1.baidu.com.
baidu.com mail is handled by 15 mx.n.shifen.com.
```

## nslookup

> Nslookup is a program to query Internet domain name servers. Nslookup has two modes: interactive and non-interactive. Interactive mode allows the user to query name servers for information about various hosts and domains or to print a list of hosts in a domain. Non-interactive mode is used to print just the name and requested information for a host or domain.

`nslookup` 用于查询DNS记录，看一下用它来查询百度域名返回的结果。

```sh
shiqiang@QKing ~ % nslookup baidu.com
Server:		192.168.31.1
Address:	192.168.31.1#53

Non-authoritative answer:
Name:	baidu.com
Address: 39.156.69.79
Name:	baidu.com
Address: 220.181.38.148

shiqiang@QKing ~ % nslookup baidu.com 8.8.8.8
Server:		8.8.8.8
Address:	8.8.8.8#53

Non-authoritative answer:
Name:	baidu.com
Address: 39.156.69.79
Name:	baidu.com
Address: 220.181.38.148
```

## dig

> dig (domain information groper) is a flexible tool for interrogating DNS name servers. It performs DNS lookups and displays the answers that are returned from the name server(s) that were queried. Most DNS administrators use dig to troubleshoot DNS problems because of its flexibility, ease of use and clarity of output. Other lookup tools tend to have less functionality than dig.

dig用来查询域名信息非常好用，一方面有大量的参数帮助控制查询行为和返回信息，甚至可以做到类似trace的功能，返回查询过程中每个节点的信息。并且dig不仅在linux中能使用，也可以在Windows中使用。可以从 [ISC](http://ftp.isc.org/isc/) 这个FTP上找到适合你的版本。

使用 `dig` 命令来查询百度域名。

```sh
shiqiang@QKing ~ % dig baidu.com

; <<>> DiG 9.10.6 <<>> baidu.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 18720
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;baidu.com.			IN	A

;; ANSWER SECTION:
baidu.com.		69	IN	A	220.181.38.148
baidu.com.		69	IN	A	39.156.69.79

;; Query time: 8 msec
;; SERVER: 192.168.31.1#53(192.168.31.1)
;; WHEN: Wed Oct 21 22:31:40 CST 2020
;; MSG SIZE  rcvd: 70


```

查询根服务器

```sh
$ dig

; <<>> DiG 9.10.6 <<>>
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52195
;; flags: qr rd ra; QUERY: 1, ANSWER: 13, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;.				IN	NS

;; ANSWER SECTION:
.			348449	IN	NS	d.root-servers.net.
.			348449	IN	NS	a.root-servers.net.
.			348449	IN	NS	m.root-servers.net.
.			348449	IN	NS	i.root-servers.net.
.			348449	IN	NS	f.root-servers.net.
.			348449	IN	NS	c.root-servers.net.
.			348449	IN	NS	h.root-servers.net.
.			348449	IN	NS	l.root-servers.net.
.			348449	IN	NS	k.root-servers.net.
.			348449	IN	NS	e.root-servers.net.
.			348449	IN	NS	j.root-servers.net.
.			348449	IN	NS	b.root-servers.net.
.			348449	IN	NS	g.root-servers.net.

;; Query time: 75 msec
;; SERVER: 172.20.10.1#53(172.20.10.1)
;; WHEN: Fri Mar 12 13:09:42 CST 2021
;; MSG SIZE  rcvd: 239

```

带上跟踪标志的查询

```sh
$ dig baidu.com +trace d2

; <<>> DiG 9.10.6 <<>> baidu.com +trace d2
;; global options: +cmd
.			24282	IN	NS	e.root-servers.net.
.			24282	IN	NS	c.root-servers.net.
.			24282	IN	NS	b.root-servers.net.
.			24282	IN	NS	a.root-servers.net.
.			24282	IN	NS	i.root-servers.net.
.			24282	IN	NS	j.root-servers.net.
.			24282	IN	NS	h.root-servers.net.
.			24282	IN	NS	g.root-servers.net.
.			24282	IN	NS	d.root-servers.net.
.			24282	IN	NS	k.root-servers.net.
.			24282	IN	NS	f.root-servers.net.
.			24282	IN	NS	m.root-servers.net.
.			24282	IN	NS	l.root-servers.net.
;; Received 239 bytes from 172.20.10.1#53(172.20.10.1) in 5 ms

com.			172800	IN	NS	a.gtld-servers.net.
com.			172800	IN	NS	b.gtld-servers.net.
com.			172800	IN	NS	c.gtld-servers.net.
com.			172800	IN	NS	d.gtld-servers.net.
com.			172800	IN	NS	e.gtld-servers.net.
com.			172800	IN	NS	f.gtld-servers.net.
com.			172800	IN	NS	g.gtld-servers.net.
com.			172800	IN	NS	h.gtld-servers.net.
com.			172800	IN	NS	i.gtld-servers.net.
com.			172800	IN	NS	j.gtld-servers.net.
com.			172800	IN	NS	k.gtld-servers.net.
com.			172800	IN	NS	l.gtld-servers.net.
com.			172800	IN	NS	m.gtld-servers.net.
com.			86400	IN	DS	30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CF C41A5766
com.			86400	IN	RRSIG	DS 8 1 86400 20210324170000 20210311160000 42351 . Vcwn1/pEqX4zjsyr5Py+PFgKGItxW75kcv8/211qf++2dR754MK7JHKe 5h9c6hsYAAIRxv8VtQt/q19bxxbEz8tj2ecrPB1gxkW2C/7FRb3laSk1 nF8rrawMZaC/qFg21JpreF89xGer5B/cEKV58WEyTJu7Z+hoPP+KQGgI y+BYM9zfr9gXml6uwJRUaqOybtRKfR9ydE0Upwc1ZEaoIkzbDJz8RqZz s5fY4uz7YdODSZqvxnghepQOphFJnHN2SFsQdQ7X9PyGSFkSTiXkA8uh zJEO6n0SgVrX2GP6ljvw21/LY1uksVtN2acQ4dXnNzLhAeeYo/xRB9lx TAkT+g==
;; Received 1169 bytes from 198.41.0.4#53(a.root-servers.net) in 281 ms

baidu.com.		172800	IN	NS	ns2.baidu.com.
baidu.com.		172800	IN	NS	ns3.baidu.com.
baidu.com.		172800	IN	NS	ns4.baidu.com.
baidu.com.		172800	IN	NS	ns1.baidu.com.
baidu.com.		172800	IN	NS	ns7.baidu.com.
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN NSEC3 1 1 0 - CK0Q1GIN43N1ARRC9OSM6QPQR81H5M9A  NS SOA RRSIG DNSKEY NSEC3PARAM
CK0POJMG874LJREF7EFN8430QVIT8BSM.com. 86400 IN RRSIG NSEC3 8 2 86400 20210317044152 20210310043152 58540 com. pG8wfg9t0VCiLMi12MzGkhzJ59IQ/q6DjDM+BL55pm677+CQrsZkyZJC B6YOxOQojPQmV1HklP4H16tCumtxRbBE3ECtCDBJYsfBLq9CRQnv3t72 yrkJkhkK5kWM6n6C59KKCUnFyJ6gtROYJQEU50ZNzaiFn0jZcMIIuBfG LVhLTcOUpo+QZRw1IXvvmjdHf3yO+WZYZNwRYYzfrhPyEw==
HPVUSBDNI26UDNIV6R0SV14GC3KGR4JP.com. 86400 IN NSEC3 1 1 0 - HPVVN3Q5E5GOQP2QFE2LEM4SVB9C0SJ6  NS DS RRSIG
HPVUSBDNI26UDNIV6R0SV14GC3KGR4JP.com. 86400 IN RRSIG NSEC3 8 2 86400 20210317061829 20210310060829 58540 com. q0e23WSidBWy2d3qd1XBwsH+kc/JMcYxaskkyR9/GRWv8PA2oKSeU7QP qQcAQC4hoswc7SqZcoGRyy3HFGC4TrJ544oN+ldLsh075ncHkurQX1De qcZsMM3OmXeWyMyV/PvRe8QsJPOvoCSGNqwDZdXxmesl2OCNxyQ7dfgz K7XCn+9sVtz3V++v3bjo18e+qnJS8+IDFmY1BTHumUkFuQ==
;; Received 757 bytes from 192.35.51.30#53(f.gtld-servers.net) in 137 ms

baidu.com.		600	IN	A	220.181.38.148
baidu.com.		600	IN	A	39.156.69.79
baidu.com.		86400	IN	NS	ns4.baidu.com.
baidu.com.		86400	IN	NS	ns2.baidu.com.
baidu.com.		86400	IN	NS	ns3.baidu.com.
baidu.com.		86400	IN	NS	ns7.baidu.com.
baidu.com.		86400	IN	NS	dns.baidu.com.
;; Received 240 bytes from 110.242.68.134#53(ns1.baidu.com) in 47 ms

;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NXDOMAIN, id: 37649
;; flags: qr rd ra; QUERY: 1, ANSWER: 0, AUTHORITY: 1, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;d2.				IN	A

;; AUTHORITY SECTION:
.			3600	IN	SOA	a.root-servers.net. nstld.verisign-grs.com. 2021031101 1800 900 604800 86400

;; Query time: 5 msec
;; SERVER: 172.20.10.1#53(172.20.10.1)
;; WHEN: Fri Mar 12 13:11:38 CST 2021
;; MSG SIZE  rcvd: 106
```



## whois

在域名使用过程中，经常会需要查询域名的注册信息，如注册人人、注册时间，以及我们通常最关注的注册的DNS，这时可以使用 `whois` 命令来满足我们的需求。

> The whois utility looks up records in the databases maintained by several Network Information Centers (NICs). By default whois starts by querying the Internet Assigned Numbers Authority (IANA) whois server, and follows referrals to whois servers that have more specific details about the query name. The IANA whois server knows about IP address and AS numbers as well as domain names.  

使用 `whois` 命令查询百度的域名。

```shell
shiqiang@QKing ~ % whois www.baidu.com
% IANA WHOIS server
% for more information on IANA, visit http://www.iana.org
% This query returned 1 object

refer:        whois.verisign-grs.com

domain:       COM

organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston Virginia 20190
address:      United States

contact:      administrative
name:         Registry Customer Service
organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston Virginia 20190
address:      United States
phone:        +1 703 925-6999
fax-no:       +1 703 948 3978
e-mail:       info@verisign-grs.com

contact:      technical
name:         Registry Customer Service
organisation: VeriSign Global Registry Services
address:      12061 Bluemont Way
address:      Reston Virginia 20190
address:      United States
phone:        +1 703 925-6999
fax-no:       +1 703 948 3978
e-mail:       info@verisign-grs.com

nserver:      A.GTLD-SERVERS.NET 192.5.6.30 2001:503:a83e:0:0:0:2:30
nserver:      B.GTLD-SERVERS.NET 192.33.14.30 2001:503:231d:0:0:0:2:30
nserver:      C.GTLD-SERVERS.NET 192.26.92.30 2001:503:83eb:0:0:0:0:30
nserver:      D.GTLD-SERVERS.NET 192.31.80.30 2001:500:856e:0:0:0:0:30
nserver:      E.GTLD-SERVERS.NET 192.12.94.30 2001:502:1ca1:0:0:0:0:30
nserver:      F.GTLD-SERVERS.NET 192.35.51.30 2001:503:d414:0:0:0:0:30
nserver:      G.GTLD-SERVERS.NET 192.42.93.30 2001:503:eea3:0:0:0:0:30
nserver:      H.GTLD-SERVERS.NET 192.54.112.30 2001:502:8cc:0:0:0:0:30
nserver:      I.GTLD-SERVERS.NET 192.43.172.30 2001:503:39c1:0:0:0:0:30
nserver:      J.GTLD-SERVERS.NET 192.48.79.30 2001:502:7094:0:0:0:0:30
nserver:      K.GTLD-SERVERS.NET 192.52.178.30 2001:503:d2d:0:0:0:0:30
nserver:      L.GTLD-SERVERS.NET 192.41.162.30 2001:500:d937:0:0:0:0:30
nserver:      M.GTLD-SERVERS.NET 192.55.83.30 2001:501:b1f9:0:0:0:0:30
ds-rdata:     30909 8 2 E2D3C916F6DEEAC73294E8268FB5885044A833FC5459588F4A9184CFC41A5766

whois:        whois.verisign-grs.com

status:       ACTIVE
remarks:      Registration information: http://www.verisigninc.com

created:      1985-01-01
changed:      2017-10-05
source:       IANA

# whois.verisign-grs.com

No match for domain "WWW.BAIDU.COM".
>>> Last update of whois database: 2020-10-24T12:34:29Z <<<

```

本文仅对 `host` 、`nslookup` 、`dig` 、`whois` 四个命令做了简单的介绍，通过这四个命令能够让我们初步掌握一些域名相关的分析技术，但是仍有更多的域名相关的技术本文没有涉及，希望大家能够自己主动探索，加深对与域名的理解和认识。

## 参考资料

1. [我的Linux工具集](http://linux.cn/home-space-uid-3-do-blog-id-107.html)
2. [dig命令详解](https://www.cnblogs.com/machangwei-8/p/10353216.html)
3. [nslookup 命令详解](https://blog.csdn.net/xg_ren/article/details/80782338)
4. [whois百度百科](https://baike.baidu.com/item/whois/280037?fr=aladdin)

