---
title: 离线环境安装使用 Ansible
date: 2022-01-04 19:56:25
keywords: ansible, 离线, 离线环境, ansible 安装
description: 本文介绍了在离线环境中如何通过源码包安装 ansible 所需要的依赖和 ansible，并简单介绍了 ansible 的使用方式。
---

之前写了一篇[介绍 Ansible 的文章](http://www.edulinks.cn/2021/07/04/20210706-ansible-startup/) ，今天回顾看来写的有些匆忙，一些具体的操作步骤都没有讲明白，不利于读者复现学习。最近又申请了一个几百台机器的环境，正好借此机会把如何在离线环境中使用 Ansible 详细记录一下。

## 使用前准备

本机环境是 Python 2.7，操作系统版本是 Red Hat Enterprise Linux Server release 7.6 (Maipo)。 

* 系统必须安装了 gcc，找一个内网的 yum 源安装 `yum install gcc` 或使用 rpm 包进行安装
* 有些依赖包编译需要依赖 python 库，因此必须安装 python-devel ，安装方法同上
* 安装 cffi 需要依赖 libffi-devel，安装方法同上
* 安装 cryptography-2.6.1.tar.gz 依赖 openssl-devel

总结下来，必须使用本地 yum 源安装几个必须的依赖。

```sh
$ yum install -y python-devel openssl-devel gcc libffi-devel
```

## 安装过程

ansible 安装需要先将 18 个依赖包安装完成，依赖包的安装过程大同小异，都是解压文件后，通过 `python setup.py install` 命令进行安装。相关的离线包可以到 [https://pypi.org/](https://pypi.org/) 上搜索后下载。

* 安装 setuptools-41.1.0.zip
* 安装 pycrypto-2.6.1.tar.gz
* 安装 PyYAML-5.1.tar.gz ，这个编译安装依赖 _yaml.h ，如果没有会转为纯 python 模式
* 安装 MarkupSafe-1.1.1.tar.gz 
* 安装 Jinja2-2.10.1.tar.gz 
* 安装 simplejson-3.16.0.tar.gz 
* 安装 pycparser-2.19.tar.gz
* 安装 cffi-1.12.3.tar.gz
* 安装 ipaddress-1.0.22.tar.gz 
* 安装 six-1.12.0.tar.gz
* 安装 asn1crypto-0.24.0.tar.gz
* 安装 idna-2.8.tar.gz
* 安装 pyasn1-0.4.5.tar.gz
* 安装 PyNaCl-1.3.0.tar.gz 
* 安装 enum34-1.1.8.tar.gz
* 安装 cryptography-2.6.1.tar.gz 
* 安装 bcrypt-3.1.6.tar.gz
* 安装 paramiko-2.4.2.tar.gz 
* 安装 ansible-2.9.7.tar.gz 

完成后，验证安装结果。推荐大家使用 Github 上 [ghl1024](https://github.com/ghl1024/ansible-offline-install/blob/main/ansible/install.sh) 整理的一个安装脚本来自动化这个过程，如果需要特定的版本，自己修改脚本内容就可以。

```sh
$ ansible --version
```

## 配置与使用

使用 ansible 操作目标主机的方式有两种，一种是通过配置主机列表后在命令行中通过主机列表名称选择设备。例如：

```sh
$ ansible machinelist -m command -a 'cat /etc/redhat-release'
```

这里的 machinelist 对应 /etc/ansible/hosts 文件中的一组机器列表

```
[machinelist]
10.2.1.1
10.2.1.2
```

使用这种方式配置的机器列表，需要安装了 ansible 的这台机器与列表中的主机都做了 ssh 互信。

我们可以利用 sshpass 这个应用来实现使用用户密码登录，这种方式要求安装了 ansible 的机器上先要安装 sshpass 。在离线环境下，推荐大家通过 rpm 包进行安装或者编译安装。

安装完成后，修改 hosts 文件配置如下。

```
[machinelist]
10.2.1.1 ansible_ssh_user=root ansible_ssh_pass=xxxxxx
10.2.1.2 ansible_ssh_user=root ansible_ssh_pass=xxxxxx
```

利用这种方式，可以实现同口令主机的批量快速访问。根据安全要求，后续口令要按照规则进行修改。最长久的办法，还是对主机做下互信配置。可以通过 `ssh-copy-id` 结合 ansible 实现主机的批量互信。

> 两种方式的配置可以混合使用。

在配置了 ssh_pass 的情况下，再介绍下如何批量添加服务器互信。

```sh
# 编写 pushssh.yaml 文件
---
  - hosts: unit_test
    user: shiqiang # 执行 ansible 的用户
    tasks:
      - name: ssh-copy
        authorized_key: user=root key="{{ lookup('file', '/home/shiqiang/.ssh/id_rsa.pub')}}"
```

上面这个配置表示 将当前用户 `shiqiang` 的 id_rsa.pub 拷贝到远程主机的 root 用户下。

```sh
shiqiang@host: ansible-playbook pushssh.yaml
# 执行完成后，可以尝试免密登录目标主机
shiqiang@host: ssh root@10.2.1.2
```

## 题外话

在使用 ansible 向多台主机发送命令的过程中，有些主机有以下的 WARNING 提示。

> [WARNING]: Platform linux on host -.-.-.- is using the discovered Python interpreter at /usr/bin/python, but future installation of another Python interpreter could change this. See https://docs.ansible.com/ansible/2.9/reference_appendices/interpreter_discovery.html for more information.

看文字的大意是某些机器上的 python 是在 `/usr/bin/python` 下的，未来可能会被替换。

忽略这个错误的办法是在 `/etc/ansible/ansible.cfg` 中添加以下配置。

```cfg
[defaults]
interpreter_python = auto_legacy_silent
```



## 参考资料

1. [Ansible 离线安装](https://www.cnblogs.com/ghl1024/p/14309382.html)
2. [ansible的安装配置和配合sshpass的使用](https://blog.csdn.net/z960339491/article/details/84328904)
3. [sshpass 绕过ssh 密码交互式验证](https://blog.csdn.net/weixin_30360497/article/details/96901120)
4. [Ansible 实现批量建立互信](https://blog.csdn.net/bruce_6/article/details/82019519)
5. [Platform linux on host is using the discovered Python interpreter](https://blog.csdn.net/u014262328/article/details/107668984)
