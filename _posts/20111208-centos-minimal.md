---
title: Centos6最小化安装中设置网卡默认启动
date: 2011-12-08 14:02:01
tag: 
---

Centos 6.0版本提供了一个"最小化"（Minimal）安装的选项。这是一个非常好的改进，因为系统中再也不会存在那些不必要的文件和服务，这样我们就可以实现服务器为我们所需要的服务提供最大的资源能力。


Minimal的安装也带来了一些缺点，系统默认提供的网络管理工具 NetworkManager 实际上没有安装在 Minimal 的系统上。这样我们就不得不手工编辑网卡的配置文件。典型的网卡配置文件（/etc/sysconfig/network-script/ifcfg-X）格式如下：


IPADDR=192.168.0.33
BOOTPROTO=none
NETMASK=255.255.255.0
GATEWAY=192.168.0.1
DNS1=192.168.0.1
DNS2=192.168.0.2
USERCTL=yes
ONBOOT="yes"
Centos 6的Minimal下，默认onboot="no"，并且设置了依赖 NetworkManager 的选项，NM_CONTROLLED="yes"。这个配置看起来有点可笑，因为minimal的情况下并没有安装 NetworkManger。所以我们需要修改为：
ONBOOT="yes"      MM_Controlled="no"

同时，如果需要自动获取IP，我们还应该加上


BOOTPROTO="dhcp"


现在重启一下，网卡就可以随机启动了。


参考资料：
1、[Netowork Configuration in minimal Linux installs](http://diznix.com/2011/07/11/network-configuration-in-minimal-linux-installs/)
2、[Configuring the ethernet nic in centos 6](http://it.toolbox.com/blogs/linux-gnu/configuring-the-ethernet-nic-in-centos-6-rhel-6-scientific-linux-6-48390)
3、[Troubleshooting Linux Connections](http://home.roadrunner.com/%7Ecomputertaijutsu/wireless.html)
4、[克隆Centos6虚拟机后网络无法启动问题解决](http://226617.cn/archives/929.htm)












