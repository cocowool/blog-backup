---
title: Mac OS路由设置常用命令
date: 2020-05-19 13:12:17
tags: Mac
---

> 关注路由配置的原因是因为单位办公环境不允许访问互联网，在原来只有一块网卡的时候，不得不频繁的在办公网和互联网之间切换。为了解决这个问题，我在Macbook Pro上外接了一块USB无线网卡，通过两块网卡实现既能上办公网，也能上互联网。

<!-- more -->

## 查看路由

Mac下通过`netstat`命令查看路由表，可以看到有两条默认路由。一般情况下，我们可以通过系统配置中的`设置网络顺序`将提供互联网接入的网卡放在第一位就能够顺利的实现既能访问互联网，也能访问办公网。

```sh
$ netstat -nr
Routing tables

Internet:
Destination        Gateway            Flags        Refs      Use   Netif Expire
default            172.20.10.1        UGSc           34       32     en5
default            11.136.160.254     UGScI           0        0     en1
```

但是在我的场景中，单位内部有一个比较特殊的地址段`128.192.*.*`因为属于B类地址，走了第一条路由导致我没有办法访问这个网段的所有应用，这时就需要单独指定对于这个网段走第二条路由。

路由信息中`Flags`的一些具体含义如下：

* U = Up
* G = Gateway
* S = Static, e.g., default route added at boot time
* H = Host-specific
* C = Generate new (host-specific) routes on use
* L = Valid link-layer (MAC) address
* c = Cloned route
* R = Reject route, known but unreachable route

## route命令

首先来介绍一下Mac下的路由命令，命令的用法如下。

```sh
$ route [-dnqtv] command [[modifiers] args]
```

支持的命令包括：`add`、`flush`、`delete`、`change`、`get`、`monitor`。

对于`monitor`和`flush`命令，后面不需要额外的参数，对于`add`、`delete`、`change`、`get`参数，则需要按照以下格式输入：

```sh
$ route [-n] command [-net | -host] [-ifscope boundif] destination gateway [netmask]
```

### 监控路由变化

```sh
$ route -v monitor
```

通过这个命令可以看到系统中路由变化的情况，如果此时在系统管理面板中调整网络的顺序，就能够看到路由变化的细节。如下所示，可以看到因为我调整了网络顺序，系统先是把两条默认路由删掉，然后按照调整的顺序再依次添加到路由表中，从而实现了网络顺序的调整。

```sh
$ route -v monitor

got message of size 164 on Tue May 19 12:20:05 2020
RTM_DELETE: Delete Route: len 164, pid: 62, seq 262, errno 0, flags:<GATEWAY,DONE,STATIC,PRCLONING,CONDEMNED>
locks:  inits: 
sockaddrs: <DST,GATEWAY,NETMASK,IFP,IFA>
 default 172.20.10.1 default en5:40.a5.ef.41.9f.fb 172.20.10.3

got message of size 164 on Tue May 19 12:20:05 2020
RTM_DELETE: Delete Route: len 164, pid: 62, seq 263, errno 0, ifscope 6, flags:<GATEWAY,DONE,STATIC,PRCLONING,IFSCOPE,CONDEMNED>
locks:  inits: 
sockaddrs: <DST,GATEWAY,NETMASK,IFP,IFA>
 default 11.136.160.254 default en1:e4.ce.8f.23.c5.de 11.136.160.44

got message of size 176 on Tue May 19 12:20:16 2020
RTM_ADD: Add Route: len 176, pid: 62, seq 270, errno 0, flags:<UP,GATEWAY,DONE,STATIC>
locks:  inits: 
sockaddrs: <DST,GATEWAY,NETMASK,IFP,IFA>
 default 11.136.160.254 default  11.136.160.44

got message of size 176 on Tue May 19 12:20:16 2020
RTM_ADD: Add Route: len 176, pid: 62, seq 271, errno 0, ifscope 5, flags:<UP,GATEWAY,DONE,STATIC,IFSCOPE>
locks:  inits: 
sockaddrs: <DST,GATEWAY,NETMASK,IFP,IFA>
 default 172.20.10.1 default  172.20.10.3
```

### 添加路由

添加路由时，`-host` 和`-net`是可选参数。如果没有加参数，并且输入的ip是一个段网络地址，则会假设为一个地址段。如果是明确的ip地址，则会对应到主机。这部分内容可以通过`man route`查看到。

```sh
$ sudo route -v add ip gateway [-netmask net-mask-ip]
# -net 参数可以指定添加一个网段
$ sudo route -v add -net 128.192 gateway
# -host 参数可以指定添加一个具体的地址
$ sudo route -v add -host 128.192.214.29 gateway
```

### 删除路由

```sh
$ sudo route -v delete ip
```

### 清空路由表

```sh
$ sudo route -v flush
```

## 持久化路由的设置

持久化路由设置需要介绍一个新的命令`networksetup`，该命令相当于系统偏好设置中的网络设置工具的命令行版本，但是自爱系统偏好设置中没有提供路由维护的功能，因此这个命令行工具提供了比系统便好设置更强大的功能，主要有以下几个方面。

### 查看所有的网络连接方式

```sh
$ networksetup -listallnetworkservices
An asterisk (*) denotes that a network service is disabled.
802.11n NIC
Wi-Fi
Ethernet
FireWire
Bluetooth PAN
Thunderbolt Bridge
iPhone USB
Parallels Shared #0
```

### 查看某个网络连接的路由信息

```sh
$ networksetup -getadditionalroutes Wi-Fi
There are no additional IPv4 routes on Wi-Fi.
```

### 添加路由信息

```sh
$ networksetup -setadditionalroutes Wi-Fi 11.33.0.0 255.255.255.0 11.136.167.254
$ networksetup -getadditionalroutes Wi-Fi
11.33.0.0 255.255.255.0 11.136.167.254
```

### 删除路由信息

```sh
$ networksetup -setadditionalroutes Wi-Fi 
$ networksetup -getadditionalroutes Wi-Fi
There are no additional IPv4 routes on Wi-Fi.

```

清空路由表就是不需要添加任何路由信息就可以。

## 参考资料

1. [MacOS上路由表的操作](https://www.baidu.com/link?url=E9XstvqW5Vbeju068bPVl_1O1YUpOLqwPEGn3ISXzyfkoLC4z38FYkr02YJ7717n7RPsOhIQyInCRU1Wgk0uAq&wd=&eqid=eae92997001fc824000000035ec23824)
2. [MacOS route flags](https://www.xuebuyuan.com/2213546.html)
3. [Mac OSX常用网络命令](https://www.cnblogs.com/linusflow/p/7390566.html)
4. [https://www.cnblogs.com/hanyifeng/p/8195538.html](https://www.cnblogs.com/hanyifeng/p/8195538.html)

