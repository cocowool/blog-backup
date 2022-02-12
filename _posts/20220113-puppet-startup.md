---
title: Puppet 安装使用体验
date: 2022-01-13 08:35:04
keywords:
description: 
---

前两天写了一篇文章介绍 [Ansible 的安装与使用](http://www.edulinks.cn/2022/01/04/20220104-run-ansible-offline/) 。最近这两天也想研究下 Puppet 比较两者的区别，在此记录下使用过程。

> Puppet 是基于 Ruby 开发的服务器配置管理系统，它支持以描述语言的方式管理大批量的系统配置。

服务器规划，操作系统为 Red Hat Enterprise Linux Server release 7.4，安装测试的 Puppet 版本为 3.8.7。

* 128.180.34.198 规划为Puppet Master，主机名为 master.puppet.cn
* 128.180.34.104 主机名为 client1.puppet.cn，规划为普通的被管服务器
* 128.180.34.105 主机名为 client2.puppet.cn，规划为普通的被管服务器

## Server 安装配置

```sh
# 安装 Puppet Server 和 Puppet，我这里安装的是3.8.7的版本
$ yum install puppet-server puppet -y

# 修改主机名配置，在 hosts 中添加以下内容
$ vim /etc/hosts
128.180.34.198 master.puppet.cn
128.180.34.104 client1.puppet.cn
128.180.34.105 client2.puppet.cn

# 启动 Puppet 服务
$ systemctl enable puppetmaster.service
$ systemctl start puppetmaster.service
```

## Agent 安装配置

首先安装客户端。

```sh
$ yum install puppet -y
```

修改主机名配置，保持与 master 一致。

```sh
$ vim /etc/hosts
128.180.34.198 master.puppet.cn
128.180.34.104 client1.puppet.cn
128.180.34.105 client2.puppet.cn
```

编辑 puppet 配置文件，默认位于 `/etc/puppet/puppet.conf` 。

```sh
[main]
    server = master.puppet.cn
```

主要增加了服务端地址的配置。

## 使用场景

后面的几个步骤需要在客户端和服务端之间切换操作。

首先在客户端开始申请证书。

```sh
root@client $ puppet agent --server=master.puppet.cn --no-daemonize --verbose
```

看到执行成功的提示后，切换到服务端进行操作。

```sh
# 查看收到的客户端证书申请列表
root@master $ puppet cert list
# 对所有的客户端申请进行批量授权
root@master $ puppet cert sign --all
# 通过目录查看已经注册成功的客户端
root@master $ ll /var/lib/puppet/ssl/ca/signed
```

所有这些完成后，可以用一个文件分发的场景来做验证，这个场景的主要目的是将服务端的一个文件分发到所有被管机上。

在 `/etc/puppet/mainfests/` 目录下创建一个 `site.pp` 文件，具体内容如下：

```
file {"/tmp/puppet.txt":
	content => "This is file for puppet distribution.",
}
```

到被管机上执行下面的命令

```sh
$ puppet agent --server=master.puppet.cn --no-daemonize --verbose --test
```







## Ansible 是否支持的场景

* 配置系统服务并设置为开机启动
* 



从组织角度思考开源软件需要落地的几个要素

* 用户认证。每家单位一般都有自己的用户认证和管理体系，对于人数超过一千的单位来说，新的系统进来不可能全部手工录入或导入这些数据。因为这种方式能搞定存量，却搞不定增量，不应当花费额外的资源去做这方面的维护。
* 权限管理。权限管理与组织架构密切相关，包括业务功能、数据功能的权限划分都离不开组织架构的具体情况。每家单位的组织架构又都不尽相同，有垂直型管理的、有扁平管理的，特别是对于存在多个办公地点的公司，可能还有跨部门、跨中心的虚拟团队的组织形式。
* 流程适配。在小的团队里工作，很多事情通过见面聊一会儿就能解决。在大的团队中，如果没有流程来限定做事的顺序，很多事情就会向意想不到的方向发展。因此，如何把开源软件的功能和本单位的流程实际需要结合起来，这中间需要流程平台的粘合，也需要开源软件具备开放的特性。
* 安全审计。根据公司的实际情况需要选择不同的网络安全等级保护标准，这就会服务器的部署、访问提出很多具体的控制性要求。开源软件在落地的时候，这些问题必须由本地化的团队来考虑。另外，公司的审计管理也会提出很多审计方面的要求。

## 参考资料

1. [Puppet 安装部署篇](https://blog.51cto.com/215687833/1962448)
2. [Puppet 服务安装和部署](https://blog.51cto.com/u_11134648/2161486)
2. [Puppet 安装及部署](https://www.cnblogs.com/gentlemanhai/p/3529554.html)
