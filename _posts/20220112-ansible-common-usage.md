---
title: Ansible 常用使用场景
date: 2022-01-12 11:33:50
keywords: Ansible , Ansible Ad-hoc , Ansible playbook , Ansible command , Ansible Shell , Ansible Copy
description: 本文整理了 Ansible 常用模块以及常用的使用场景
---

给 Ansible 下一个完整的定义很难，官方的宣传是 `Ansible is Simple IT Automation` 看出来还是主打自动化。

> Ansible delivers simple IT automation that ends repetitive tasks and frees up DevOps teams for more strategic work.

也有资料介绍 Ansible 是模型驱动的配置管理器，个人觉得这个描述不是很恰当。从 Ansible 的使用方式来看，默认使用 push 的方式更像自动化。而 Puppet 默认使用 pull 的方式，则更像是配置驱动。

Ansible 的使用有两种模式，一种是直接在命令行调用模块批量执行单个命令，这种称为 AD HOC 模式。

```sh
$ ansible [host_inventory] -m command -a 'hostname'
```

也可以通过编写 yaml 文件，来编排一组操作，这种方式称为 playbook 模式。看一个简单的配置。

```yaml
# test.yaml
---
- hosts: 192.168.1.31
  remote_user: root
  tasks: 
    - name: run df -h
      remote_user: test
      shell: name=df -h
```

使用方式是这样，AD HOC 模式无法持久化，playbook 方式可以通过 yaml 文件实现持久化和重复执行。

```sh
$ ansible-playbook test.yaml
```

今天我们来熟悉几个最常用的模块：

## command 模块

在远程节点上执行命令，使用方式如下：

```sh
$ ansible [host_list] -m command -a "hostname"
```

但这种方式并不是调用 shell 环境去执行命令，所以无法获取环境变量，也不能使用管道符、重定向等。

支持 `chdir` 参数，即执行命令前先切换到指定目录。

```sh
$ ansible [host_list] -m command -a "ls -l chdir=/etc/yum.repos.d"
# command 是 ansible 的默认模块，可以省略
$ ansible [host_list] -a "ls -l chdir=/etc/yum.repos.d"
```

## shell 模块

如果需要在远程的 shell 下执行命令，则需要使用 shell 模块。使用方法与 command 相同，但是命令执行时调用的 `/bin/sh`

```shell
$ ansible [host_list] -m command -a 'echo $LOGNAME'
# 该命令可以打印当前登录的用户名
$ ansible [host_list] -m command -a 'ps -ef | grep java | wc -l'
# 该命令可以统计服务器上运行的 java 程序数量
```

## copy 模块

复制文件，支持从本地复制到服务端。

```sh
# 从服务端向被管机拷贝文件
$ ansible [host_list] -m copy -a 'src=/etc/hosts dest=/tmp owner=root mode=0755'

# 将内容存为远端文件
$ ansible [host_list] -m copy -a 'content=Hello World! desc/tmp/test.txt owner=root force=yes mode=0755'

# 从被管机拷贝文件到服务端
$ ansible [host_list] -m copy -a 
```

## fetch 模块

从远端获取文件，如果 `dest` 指定为文件夹时，默认为以每台服务器的IP为名称创建文件夹，相关的文件保存在对应的文件夹下。

```sh
# 从远端服务器获取文件
$ ansible [host_list] -m fetch -a 'src=/etc/hosts dest=/home/path owner=root mode=0755'
```

**参数列表**：

* `dest` ：存储目标的目录。如果获取 `/etc/hosts` 目标存放在 `/home` 下，最终保存路径是 `/home/host.example.com/etc/hosts`，主机名的依据是 `/etc/ansible/hosts` 的配置。
* `src` ：目前只能是文件。



## 参考资料

1. [Ansible 拷贝文件或目录](https://www.cnblogs.com/pzk7788/p/10213389.html)
2. [Ansible 常用模块](https://www.cnblogs.com/happy-king/p/9013312.html)
3. [Ansible 命令模块](https://blog.csdn.net/bruce_6/article/details/80743578)
3. [How Ansible Works](https://www.ansible.com/overview/how-ansible-works)
3. [Ansible之Playbook](https://www.cnblogs.com/yanjieli/p/10969299.html)
3. [Ansible 复制文件到本地](https://www.cnblogs.com/hiyang/p/13748777.html)
