---
title: Rsync服务介绍与配置
date: 2017-03-14 14:51:01
tag: 
---

本文简要介绍Rsync的使用方法

## Rsync 简要介绍
rsync 是一个用于增量文件传输的开源工具，不得不说，rsync简直是不同服务器间传输文件、同步文件的利器。与FTP相比，它具有非常简单的安装和配置方法。而且，rsync可以只拷贝发生过变化的文件，这就让传输效率非常高。如果你考虑安全问题，也可以通过**ssh**协议来进行传输。**rsync**用来进行备份和镜像也非常棒，它的这些特性可能是你非常需要的：
> 支持链接、属主、属组合权限的拷贝
支持包含或排出条件，类似**tar**
不需要 root 权限
支持匿名或授权的 rsync server 模式


## 典型用法
rsync 可以用来同步本地与远程服务器，包括将本地文件上传至远程服务器、将远程文件同步到本地等等。
```rsync local-file user@remote-host:remote-file

```
> 使用方法
rsync [OPTION] … SRC … [USER@]HOST:DEST
rsync [OPTION] … [USER@]HOST:SRC [DEST]
rsync --verbose  --progress --stats --compress --rsh=/usr/local/bin/ssh
--recursive --times --perms --links --delete
--exclude "**bak" --exclude "**~"
/www/* webserver:simple_path_name


## 守护进程 rsync —daemon
以守护模式启动rsync的命令为
```bash
rsync -daemon
```
守护进程必须使用具有root用户权限的用户启动，如果使用其他用户启动，必须确保有相应的数据、日志文件权限。
> Note that you should not send the rsync daemon a HUP signal to force it to reread the rsyncd.conf file. The file is re-read on each client connection.


## 配置文件 rsyncd.conf
文件包含模块和参数，模块的格式为**[module_name]**，模块包含的内容至到下一个模块开始结束。配置文件是基于行的，每一行可能是模块名称、注释或者参数。

### 全局参数
出现在配置文件前面的内容为全局参数，rsync也允许使用**[global]**的形式来指定全局参数。
**1. motd file**
表示message of the day文件配置，文件中的内容会显示在每个链接建立后。具体形式为**motdfile=FILE**
**2. pid file**
为rsync指定pid。
**3. port**
默认端口为873，可以在配置文件中重新指定。
**4. address**
可以指定rsync监听的地址。

### 模块参数
**1. comment**
默认为空，展示在用户获取所有的模块后，显示模块的描述信息。
**2. path**
该参数必须设置，指定模块对应的文件夹位置。
**3. use chroot**
**4. max connections**
设置同时链接的数量。
**5. log file**
指定日志文件位置。
**6. lock file**
默认为 /var/run/rsyncd.lock，必须与 max connections 参数同时使用。用来记录当前链接的数量。
**7. read only**
该参数指定客户端能否向服务端上传文件，默认为no。
**8. write only**
该参数指定客户端能否从服务端下载文件。

## 无密码同步的几种方法

### 1.使用daemon方式
服务端以daemon方式启动rsync。
客户端使用指定 password-file 的方式实现不用密码进行同步，如下：
```bash
rsync -arv —password-file=/etc/rsyncd.secrets /local_file user@remote_host::remote_module
```

### 参考资料
1、[rsyncd.conf](https://rsync.samba.org/ftp/rsync/rsyncd.conf.html)
2、[rsync无密码同步方法](http://www.tuicool.com/articles/bUZJj2u)
3、[rsync tutorial](http://everythinglinux.org/rsync/)
4、[wiki rsync](https://en.wikipedia.org/wiki/Rsync)
5、[How rsync works](https://rsync.samba.org/how-rsync-works.html)












