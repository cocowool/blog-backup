---
title: 注意了！这样用 systemd 可能会有风险
date: 2022-08-11 16:03:40
description: 在 Linux 6 / CentOS 6 中，使用 service 来进行服务的起停，但是在 Linux 7 / CentOS 7 中，替换为使用 systemctl 命令来控制。将一些常用应用注册成服务后，可以使用 systemctl 命令来方便的操作启动、停止、重启，但是作者最近发现如果配置不当，严重情况下可能影响正常业务运行，请大家务必关注。
---

在 Linux 6 / CentOS 6 中，使用 service 来进行服务的起停，但是在 Linux 7 / CentOS 7 中，替换为使用 systemctl 命令来控制。将一些常用应用注册成服务后，可以使用 systemctl 命令来方便的操作启动、停止、重启，但是作者最近发现如果配置不当，严重情况下可能影响正常业务运行，请大家务必关注。

```sh
$ service start loop.service
The service command supports only basic LSB actions (start, stop, restart, try-restart, reload, force-reload, status). For other actions, please try to use systemctl.
```

## 问题描述

如果我们有一些负责自动化的应用，例如 puppet / mco、ansible，这里简称为宿主服务。宿主服务注册为系统服务并且随系统开机自启动。宿主服务支持接收服务端指令并拉起一些常驻进程，拉起的进程我们简称为子进程。当宿主服务被 kill 或意外终止时，会引起子进程一起被 kill。

为了复现这个问题，我写了两个脚本。parent_pro.sh 作为宿主脚本，注册为系统 loop.service 并且随系统启动。该脚本的作用是每隔10秒钟检查 config.txt 的配置，如果配置文件中的数字变成 1 则拉起一个无限循环的子进程。

我的 service 定义信息：

```sh
[Unit]
Description=loop service
After=network.target

[Service]
Type=forking
StandardOutput=/root/namespace/parent_pro.log
StandardError=/root/namespace/parent_pro.log
WorkingDirectory=/root/namespace
User=root
Group=root
ExecStart=/usr/bin/sh parent_pro.sh

[Install]
WantedBy=multi-user.target
```

下面执行启动服务。

```sh
$ systemctl start loop.service
$ ps -ef | grep sh
root      4615     1  0 15:59 ?        00:00:00 /usr/bin/sh parent_pro.sh
root      5153  4615  0 16:00 ?        00:00:00 sh /root/namespace/child_loop.sh
root      5197  4615  0 16:00 ?        00:00:00 sh /root/namespace/child_loop.sh
root      5248  4615  0 16:00 ?        00:00:00 sh /root/namespace/child_loop.sh
```

从上面的这段输出可以看到，parent_pro.sh 已经启动了三个子进程，我们再看下 parent_pro.sh 的详细信息。

```sh
$ systemctl status
● qking-101
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Thu 2022-08-11 15:59:54 CST; 29s ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
           ├─user.slice
           │ └─user-0.slice
           │   └─session-1.scope
           │     ├─5125 sshd: root@pts/0    
           │     ├─5129 -bash
           │     ├─5224 systemctl status
           │     └─5225 less
           └─system.slice
             ├─rsyslog.service
             │ └─4618 /usr/sbin/rsyslogd -n
             ├─postfix.service
             │ ├─4944 /usr/libexec/postfix/master -w
             │ ├─4948 pickup -l -t unix -u
             │ └─4949 qmgr -l -t unix -u
             ├─loop.service
             │ ├─4615 /usr/bin/sh parent_pro.sh
             │ ├─5153 sh /root/namespace/child_loop.sh
             │ ├─5197 sh /root/namespace/child_loop.sh
             │ ├─5198 sleep 10
             │ ├─5220 sleep 1
             │ └─5222 sleep 1
```

可以看到我注册的 loop.service 在 system.slice 这个 cgroup 目录下。接下来复现问题场景。

```sh
$ kill 4615
$ ps -ef | grep sh
root      1857     2  0 15:59 ?        00:00:00 [kdmflush]
root      1873     2  0 15:59 ?        00:00:00 [kdmflush]
root      3220     2  0 15:59 ?        00:00:00 [kdmflush]
root      4613     1  0 15:59 ?        00:00:00 /usr/sbin/sshd -D
root      5125  4613  0 16:00 ?        00:00:00 sshd: root@pts/0
root      5129  5125  0 16:00 pts/0    00:00:00 -bash
root      5330  5129  0 16:00 pts/0    00:00:00 grep --color=auto sh
```

杀掉父进程之后，子进程跟着消失了。但是如果手工执行 parent_pro.sh 不会出现这个问题。

```sh
$ nohup sh parent_pro.sh > parent_pro.log  2>&1 &
$ systemctl status
● qking-101
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Thu 2022-08-11 15:59:54 CST; 2h 21min ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
           ├─user.slice
           │ └─user-0.slice
           │   ├─session-4.scope
           │   │ ├─5797 sshd: root@pts/1    
           │   │ ├─5801 -bash
           │   │ ├─6012 sh parent_pro.sh
           │   │ ├─6013 sleep 10
           │   │ ├─6014 systemctl status
           │   │ └─6015 less
           │   └─session-1.scope
           │     ├─5125 sshd: root@pts/0    
           │     └─5129 -bash
```

修改配置文件让父进程拉起一些子进程。

```sh
$ echo 0 > config.txt
$ ps -ef | grep sh
root      6012  5801  0 18:21 pts/1    00:00:00 sh parent_pro.sh
root      6018  6012  0 18:21 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6041  6012  0 18:21 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6084  6012  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6147  6012  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6232  6012  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
$ systemctl status
● qking-101
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Thu 2022-08-11 15:59:54 CST; 2h 23min ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
           ├─user.slice
           │ └─user-0.slice
           │   ├─session-4.scope
           │   │ ├─5797 sshd: root@pts/1    
           │   │ ├─5801 -bash
           │   │ ├─6012 sh parent_pro.sh
           │   │ ├─6018 sh /root/namespace/child_loop.sh
           │   │ ├─6041 sh /root/namespace/child_loop.sh
           │   │ ├─6084 sh /root/namespace/child_loop.sh
           │   │ ├─6147 sh /root/namespace/child_loop.sh
           │   │ ├─6232 sh /root/namespace/child_loop.sh
           │   │ ├─6337 sh /root/namespace/child_loop.sh
           │   │ ├─6460 sh /root/namespace/child_loop.sh
           │   │ ├─6603 sh /root/namespace/child_loop.sh
           │   │ ├─6766 sh /root/namespace/child_loop.sh
           │   │ ├─6949 sh /root/namespace/child_loop.sh
           │   │ ├─6950 sleep 10
           │   │ ├─7069 sleep 1
           │   │ ├─7071 sleep 1
           │   │ ├─7073 sleep 1
           │   │ ├─7075 sleep 1
           │   │ ├─7077 sleep 1
           │   │ ├─7079 sleep 1
           │   │ ├─7081 sleep 1
           │   │ ├─7084 sleep 1
           │   │ ├─7085 sleep 1
           │   │ ├─7087 sleep 1
           │   │ ├─7088 systemctl status
           │   │ └─7089 less
           │   └─session-1.scope
           │     ├─5125 sshd: root@pts/0    
           │     └─5129 -bash
           └─system.slice
             ├─rsyslog.service
```

这时杀掉父进程不会对子进程产生影响。

```sh
$ kill 6012
$ ps -ef | grep sh
root      6018     1  0 18:21 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6041     1  0 18:21 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6084     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6147     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6232     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6337     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6460     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6603     1  0 18:22 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6766     1  0 18:23 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      6949     1  0 18:23 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      7154     1  0 18:23 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      7377     1  0 18:23 pts/1    00:00:00 sh /root/namespace/child_loop.sh
root      7620     1  0 18:23 pts/1    00:00:00 sh /root/namespace/child_loop.sh
[1]+  Terminated              nohup sh parent_pro.sh > parent_pro.log 2>&1
$ systemctl status
● qking-101
    State: running
     Jobs: 0 queued
   Failed: 0 units
    Since: Thu 2022-08-11 15:59:54 CST; 2h 24min ago
   CGroup: /
           ├─1 /usr/lib/systemd/systemd --switched-root --system --deserialize 22
           ├─user.slice
           │ └─user-0.slice
           │   ├─session-4.scope
           │   │ ├─5797 sshd: root@pts/1    
           │   │ ├─5801 -bash
           │   │ ├─6018 sh /root/namespace/child_loop.sh
           │   │ ├─6041 sh /root/namespace/child_loop.sh
           │   │ ├─6084 sh /root/namespace/child_loop.sh
           │   │ ├─6147 sh /root/namespace/child_loop.sh
           │   │ ├─6232 sh /root/namespace/child_loop.sh
           │   │ ├─6337 sh /root/namespace/child_loop.sh
           │   │ ├─6460 sh /root/namespace/child_loop.sh
           │   │ ├─6603 sh /root/namespace/child_loop.sh
           │   │ ├─6766 sh /root/namespace/child_loop.sh
           │   │ ├─6949 sh /root/namespace/child_loop.sh
           │   │ ├─7154 sh /root/namespace/child_loop.sh
           │   │ ├─7377 sh /root/namespace/child_loop.sh
           │   │ ├─7620 sh /root/namespace/child_loop.sh
           │   │ ├─9114 sleep 1
           │   │ ├─9116 sleep 1
           │   │ ├─9119 sleep 1
           │   │ ├─9120 sleep 1
           │   │ ├─9122 sleep 1
           │   │ ├─9126 sleep 1
           │   │ ├─9127 sleep 1
           │   │ ├─9128 sleep 1
           │   │ ├─9130 sleep 1
           │   │ ├─9132 sleep 1
           │   │ ├─9134 sleep 1
           │   │ ├─9136 sleep 1
           │   │ ├─9138 sleep 1
           │   │ ├─9139 systemctl status
           │   │ └─9140 less
           │   └─session-1.scope
           │     ├─5125 sshd: root@pts/0    
           │     └─5129 -bash
           └─system.slice
```

## 问题分析

仔细查阅了 systemd 相关文档，发现这个问题主要与 type 参数有关。

Type：定义 service 的类型，主要有以下类型：

* simple：默认类型，启动的程序就是主体程序，这个程序要是退出那么一切皆休。因为 simple 类型不存在主进程退出的情况也就不存在有返回状态的情况，所以它一旦启动就认为是成功的，除非没起来。
* forking：标准 Unix Daemon 使用的启动方式。启动程序后会调用 fork() 函数，把必要的通信频道都设置好之后父进程退出，留下守护精灵的子进程。如果启动的进程能够自己创建pidfile，最好也指定下PIDFile=XXX。
* oneshot：一次性服务，这种服务类型就是启动，完成，没进程了。因为这类服务运行完就没进程了，我们经常会需要 RemainAfterExit=yes。后面配置的意思是说，即使没进程了，我们也要 Systemd 认为该服务是存在并成功了的。其他类型千万不要去设置RemainAfterExit=yes，否则systemd会认为服务启动成功了，重启或再去启动都会失败。
* dbus：这个程序启动时需要获取一块 DBus 空间，所以需要和 BusName= 一起用。只有它成功获得了 DBus 空间，依赖它的程序才会被启动。
* notify：这个程序在启动完成后会通过 sd_notify 发送一个通知消息。所以还需要配合 NotifyAccess 来让 Systemd 接收消息，后者有三个级别：none，所有消息都忽略掉; main，只接受我们程序的主进程发过去的消息; all，我们程序的所有进程发过去的消息都算。NotifyAccess 要是不写的话默认是 main。
* idle：这个程序要等它里面调度的全部其它东西都跑完才会跑它自己。比如你 ExecStart 的是个 shell 脚本，里面可能跑了一些别的东西，如果不这样的话，那很可能别的东西的控制台输出里会多一个“启动成功”这样的 Systemd 消息。

最后，如果大家的服务会处理我这种场景，请务必记住这个参数要设置好了，另外还有一个 KillMode 参数也与 cgroup 有关，当我们执行 systemctl stop loop.service 的时候，整个 cgroup 会被删除，通过设置 KillMode 参数，可以确保子进程不会被清除。

## 参考资料

1. [unary operator expected解决方法](https://blog.csdn.net/qq_34418380/article/details/122406276)
2. [Linux 系统注册系统服务流程](https://blog.csdn.net/qq_23312117/article/details/123294598)
3. [Systemd使用小结](https://juejin.cn/post/6844904029533765645)
4. [使用systemctl管理服务](https://www.cnblogs.com/outsrkem/p/11766267.html)
