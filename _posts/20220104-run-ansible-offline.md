---
title: 离线环境安装使用 Ansible
date: 2022-01-04 19:56:25
keywords:
description:
---

之前写了一篇[介绍 Ansible 的文章](http://www.edulinks.cn/2021/07/04/20210706-ansible-startup/) ，今天回顾看来写的有些匆忙，一些具体的操作步骤都没有讲明白，不利于读者复现学习。最近又申请了一个几百台机器的环境，正好借此机会把如何在离线环境中使用 Ansible 详细记录一下。

## 使用前准备

本机环境是 Python 2.7，操作系统版本是 Red Hat Enterprise Linux Server release 7.6 (Maipo)。 

* 安装包 ansible-2.9.7.tar.gz
* 系统必须安装了 gcc，找一个内网的 yum 源安装 `yum install gcc` 或使用 rpm 包进行安装
* 有些依赖包编译需要依赖 python 库，因此必须安装 python-devel ，安装方法同上
* 安装 cffi 需要依赖 libffi-devel，安装方法同上
* 安装 cryptography-2.6.1.tar.gz 依赖 openssl-devel

## 安装过程

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

完成后，验证安装结果

```sh
$ ansible --version
```

## 配置与使用

* 修改 .ansible.cfg 配置，指定 host 文件的路径

## 参考资料

1. [Ansible 离线安装](https://www.cnblogs.com/ghl1024/p/14309382.html)
2. [ansible的安装配置和配合sshpass的使用](https://blog.csdn.net/z960339491/article/details/84328904)
3. [sshpass 绕过ssh 密码交互式验证](https://blog.csdn.net/weixin_30360497/article/details/96901120)
