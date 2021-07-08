---
title: Ansible 使用体验
date: 2021-07-04 21:24:17
tags:
keywords: ansible, 自动化运维
description: 本文介绍 Ansible 的安装方法与初步的使用体验。
---

## 安装

#### 1. Yum 安装
只需要在管理机上安装，推荐使用 ```yum install ansible```。
被管理机上需要有python环境。

#### 2. 离线安装
> 本文在Red Hat Enterprise Linux Server release 7.6 上验证了离线安装 ansible 2.9.7

因为测试环境不支持互联网，因此需要在离线环境下安装 ansible。安装的过程参考了参考资料2的文章，这里只是简单复述一下过程和中间遇到的问题。

首先是安装一些前置的包，可以使用本地的 yum 源，执行 `yum install python-devel opssl-devel libffi-devel`

从官方下载源代码包  [ansible-2.9.7](https://releases.ansible.com/ansible/)，同时需要去 https://pypi.org/ 上下载依赖的模块包，具体的内容可以看参考资料2。这时体验到连网的好处，因为一个个下载安装真的是比较烦。

按照顺序先安装依赖包，最后安装 ansible ，可以看到安装成功后的提示信息

```sh
ansible --version
ansible 2.9.7
  config file = None
  configured module search path = [u'/root/.ansible/plugins/modules', u'/usr/share/ansible/plugins/modules']
  ansible python module location = /usr/lib/python2.7/site-packages/ansible-2.9.7-py2.7.egg/ansible
  executable location = /usr/bin/ansible
  python version = 2.7.5 (default, Apr 11 2018, 07:36:10) [GCC 4.8.5 20150623 (Red Hat 4.8.5-28)]
```
## 快速体验

Ansible 管理节点连接被管节点有两种方式，一种是设备间配置互信后免密登陆，这种方式的危害性很大，管理节点相当于堡垒机，一旦被攻破后果不堪设想。另一种方法是通过用户密码登陆，但是需要管理节点安装 sshpass，并且用户口令是明文写在 hosts 配置文件中的。

### 机器配置

先在配置文件中维护被管主机列表 /etc/ansible/hosts
支持多种模式，默认不分组，分组支持使用通配符匹配等。

```sh
[zookeeper]		#定义机器的分组组名
192.168.0.104 ansible_ssh_pass=password ansible_ssh_user=root
192.168.0.103 ansible_ssh_pass=password ansible_ssh_user=root
192.168.0.102 ansible_ssh_pass=password ansible_ssh_user=root
```

### 使用
ansible使用有两种方式，Ad-hoc 命令行方式和 yaml 方式。

#### Ad-hoc 方式
```sh
# ansible host|group -m module_name -a `module_parameters` ansible_parameters
[root@devops-105 software]# ansible zookeeper -m ping
192.168.0.104 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
192.168.0.102 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
192.168.0.103 | SUCCESS => {
    "changed": false, 
    "ping": "pong"
}
[root@devops-105 software]# ansible zookeeper -m command -a "uptime"
192.168.0.104 | CHANGED | rc=0 >>
 16:31:15 up  1:32,  2 users,  load average: 0.00, 0.02, 0.05

192.168.0.102 | CHANGED | rc=0 >>
 16:31:12 up 34 min,  2 users,  load average: 0.01, 0.05, 0.05

192.168.0.103 | CHANGED | rc=0 >>
 16:31:14 up  1:31,  2 users,  load average: 0.02, 0.05, 0.05
```
#### YAML 方式

yaml 方式支持通过 yaml 文件来定义复杂的操作，具体内容另外开一篇文章介绍。

### 常用模块
- ping 模块
- command 模块
- shell 模块
- yum 模块
- service 模块
- copy 模块
- unarchive 模块
- file 模块

### 参考资料
1. [Ansible Module Docs](https://docs.ansible.com/ansible/latest/modules/modules_by_category.html)
2. [CentOS7 Linux生产环境源码编译离线安装Ansible自动化运维工具](https://www.cnblogs.com/fusheng11711/p/12752901.html)
3. [Linux之间配置SSH互信](https://www.cnblogs.com/jytx/p/7272860.html)

