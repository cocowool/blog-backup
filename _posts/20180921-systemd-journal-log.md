---
title: Linux系统管理员应该知道的journalctl知识
date: 2018-09-21 11:10
tag: 
---

> 在Systemd出现之前，Linux系统及各应用的日志都是分别管理的，Systemd开始统一管理了所有Unit的启动日志，这样带来的好处就是可以只用一个 journalctl命令，查看所有内核和应用的日志。


## 1. 基础用法

### 1.1 查看所有日志(默认显示本次启动的所有日志)
```bash
[root@devops-101 ~]# journalctl
```
查看本次启动的所有日志也可以使用
```bash
[root@devops-101 ~]# journalctl -b
```

### 1.2 查看内核日志
```bash
[root@devops-101 ~]# journalctl -k
```

### 1.3 查看指定时间的日志
通过--since和--until选项，可以过滤任意时间限制，显示指定条件之前、之后或之间的日志。
```bash
[root@devops-101 ~]# journalctl --since="2018-09-21 10:21:00"
```
查询一个时间段范围内的日志。
```bash
[root@devops-101 ~]# journalctl --since="2018-09-21 10:21:00" --until="2018-09-21 10:22:00"
```

### 1.4 根据不同的主题进行过滤筛选
**根据服务筛选**
```bash
[root@devops-101 ~]# journalctl -u kubelet.service
[root@devops-101 ~]# journalctl -u kubelet
```
**根据进程ID查询**
如果进程使用了systemd托管日志，则可以通过以下命令查找进程对应的日志。
```bash
[root@devops-101 ~]# journalctl _PID=1
```
> Systemd journal 有很多可以用来过滤的字段，可以通过```man systemd.journal-fields```查看所有可以用来过滤的字段。对于用来筛选的字段，可以使用```-F```参数来查看所有可以用来过滤的值，例如```journalctl -F _PID```。

**按优先级**
操作系统提供了从0 (emerg) 到 7 (debug) 一共7个级别的日志，可以配合-p参数分别查看对应级别的日志。
```bash
[root@devops-101 ~]# journalctl -p 5 -u kubelet
```
7个级别的含义为

* 0: emerg
* 1: alert
* 2: crit
* 3: err
* 4: warning
* 5: notice
* 6: info
* 7: debug

### 1.5 调整显示输出
默认情况，journal输出进入分页模式，用户可以在终端上调整显示的内容，如果要不需要分页，需要加上```--no-pager```参数。
**以Json格式输出**
```bash
[root@devops-101 ~]# journalctl -p 5 --no-pager -o json
```
通过```-o```参数，可以设置为json格式输出，这对于其他接收json格式的日志分析工具非常友好。
```bash
[root@devops-101 ~]# journalctl -p 5 --no-pager -o json-pretty
```
使用```json-pretty```则对于管理员查看日志非常易读。
支持的各种格式如下：

* cat: 只显示信息字段本身。
* export: 适合传输或备份的二进制格式。
* json: 标准JSON，每行一个条目。
* json-pretty: JSON格式，适合人类阅读习惯。
* json-sse: JSON格式，经过打包以兼容server-sent事件。
* short: 默认syslog类输出格式。
* short-iso: 默认格式，强调显示ISO 8601挂钟时间戳。
* short-monotonic: 默认格式，提供普通时间戳。
* short-precise: 默认格式，提供微秒级精度。
* verbose: 显示该条目的全部可用journal字段，包括通常被内部隐藏的字段。

### 1.6 活动日志跟踪
journalctl也支持类似tail的功能，如通过```-n```参数指定显示最近的多少行，默认为10行，通过```-f```参数持续监控日志输出。

## 2. 维护

### 2.1 查看日志占用的磁盘空间
```bash
[root@devops-101 ~]# journalctl --disk-usage
Archived and active journals take up 8.0M on disk.
```

### 2.2 设置日志占用的空间
```bash
root@devops-101 ~]# journalctl --vacuum-size=500M
Vacuuming done, freed 0B of archived journals on disk.
```

### 2.3 设置日志保存的时间
```bash
[root@devops-101 ~]# journalctl --vacuum-time=1month
Vacuuming done, freed 0B of archived journals on disk.
```

## 3. 配置
上面的一些维护动作，也可以通过配置参数设置，配置文件位于```/etc/systemd/journald.conf```。

* Storage=persistent
* Compress=yes
* Seal=yes
* SplitMode=uid
* SyncIntervalSec=5m
* RateLimitInterval=30s
* RateLimitBurst=1000
* SystemMaxUse=
* SystemKeepFree=
* SystemMaxFileSize=
* RuntimeMaxUse=
* RuntimeKeepFree=
* RuntimeMaxFileSize=
* MaxRetentionSec=
* MaxFileSec=1month
* ForwardToSyslog=yes
* ForwardToKMsg=no
* ForwardToConsole=no
* ForwardToWall=yes
* TTYPath=/dev/console
* MaxLevelStore=debug
* MaxLevelSyslog=debug
* MaxLevelKMsg=notice
* MaxLevelConsole=info
* MaxLevelWall=emerg

## 参考资料：

1. [journal详细介绍](http://blog.51cto.com/13598893/2072212)












