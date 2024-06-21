---
title: 如何清理TimeMachine备份，释放Mac空间
date: 2024-06-14 10:58:24
keywords: mac, macOs, TimeMachine, 清理
description: 本文介绍如何使用终端命令删除 Time Machine 中的备份。
---

TimeMachine 是 MacOS 系统提供的一项方便的笔记本电脑全盘备份应用，能够帮助我们找回过去的文档，还能在尝试重新安装操作系统前做一个备份，以便快速的回推到之前的系统状态。

使用时间久了之后，TimeMachine 会占用本地磁盘空间，本文介绍如何删除 TimeMachine 不需要的历史快照，帮助电脑释放空间。

一般情况下，Time Machine 会自动清理历史的备份，在系统可用空间不足时，macOS 系统也会触发一些清理动作。但是我们仍能够选择自主的清理 Time Machine 的历史备份。


下面介绍下如何利用 Terminal 终端命令删除 Time Machine 磁盘上不再需要的备份，释放空间让备份能够正常执行。

首先，我们需要将 Time Machine 磁盘连接到 macOS 电脑，接下来打开 Terminal 终端。

```sh
# 列出所有的备份
$ tmutil listbackups
# 删除指定的备份，挂载点需要配合时间戳参数一起使用
$ sudo tmutil delete -d /Volumes/Timemachine\ HD -t 2024-03-21-080702
```

如果想删除指定的文件或者目录，需要先找到目录路径，然后使用 `-p` 参数。

```
# 列出某个目录下Time Machine的所有快照
$ tmutil listlocalsnapshots /
$ sudo tmutil delete -d /Volumes/MyBackupDisk -t 2023-03-14-100000 -p /Path/To/FileOrDirectory
```

## 参考资料

1. [How to Delete Time Machine Backups](https://www.lifewire.com/delete-time-machine-backups-5219464)