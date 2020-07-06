---
title: 在Redhat 7.3中采用离线方式安装Docker
date: 2018-07-11 18:32
tag: 
---

> 本文环境 Redhat Linux 7.3。Docker 18.


## 写在前面
Docker CE默认是不支持Redhat的，如果你想在Redhat安装，可以使用静态二进制包。这是我多次尝试RPM后得出的结论。使用这种方式安装，不支持32位的操作系统。这种方式仅仅是为了在测试环境能够运行Docker，真正的生产环境，建议大家还是使用Docker支持的操作系统或是选择企业版本。
静态二进制的Daemon仅支持Linux（dockerd）、Client支持Linux和Mac（docker）。

## 系统要求
安装需满足以下要求：

* 64位操作系统
* 3.1及以上的Linux内核
* iptables版本在1.4及以上
* git版本在1.7及以上
* A ps executable, usually provided by procps or a similar package.
* XZ Utils 4.9 or higher
* A properly mounted cgroupfs hierarchy; a single, all-encompassing cgroup mount point is not sufficient.

## 安装
1、下载二进制文件
从[这里](https://download.docker.com/linux/static/stable/x86_64/)下载所需版本的tgz文件。
2.、解压文件
```bash
[root@devops-102 ~]# tar -xvzf docker-18.03.1-ce.tgz
```
3、将文件移动到系统目录下 excutable path。本步骤为可选步骤，也可以把解压路径放到PATH变量中。
```bash
$ sudo cp docker/* /usr/bin/
```
执行后会看到API listen on /var/run/docker.sock  的提示，表示docker已经在运行了。
4、启动Dockerd
```bash
$ sudo dockerd &
```
5、验证
```bash
$ sudo docker run hello-world
```
能够看到输出 Hello from Docker! 就OK了。


## 参考资料

1. [Install Docker CE from binaries](https://docs.docker.com/install/linux/docker-ce/binaries/#next-steps)












