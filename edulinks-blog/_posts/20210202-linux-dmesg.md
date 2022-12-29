---
title: dmesg命令介绍
date: 2021-02-02 08:40:12
tags:
keywords: dmesg, linux dmesg
description: dmesg命令用于显示开机信息，在需要排查系统问题时，非常有用。
---

## 命令介绍

dmesg命令用于显示开机信息，运行 dmesg 命令需要 root 权限，dmesg 命令在多数基于 Linux 和 Unix 的操作系统中都可以使用。

dmesg 命令提供的信息在设备故障诊断时非常重要，因为 dmesg 命令输出的信息非常多，因此我们常需要配合管道、查询命令一起使用，或者将信息输出到文本中进行检索分析。

kernel会将开机信息存储在ring buffer中。您若是开机时来不及查看信息，可利用dmesg来查看。开机信息也会保存在 /var/log 目录中名称为 dmesg 的文件里。

## 语法

```sh
$ dmesg [-cn][-s <缓冲区大小>]
# -c 显示信息后，清除 ring buffer 中的内容
# -n 设置记录信息的层级
# -s<缓冲区大小> 

```

dmesg 定义了八个不通的错误级别，可以使用 `dmesg --level=err,warn` 设定，具体的级别如下：

* emerg ： 系统无法使用
* alert ：必须立即采取行动
* crit ：危急的错误
* err ：一般错误
* warn ：警告类型的错误
* notice ：正常信息但值得关注
* info ：普通信息
* debug ： 调试级别的信息

看一下具体的例子：

```sh
$ sudo dmesg --level=err                                                                                                                                       
[    2.060380] piix4_smbus 0000:00:07.3: SMBus Host Controller not enabled!
[    2.635310] sd 2:0:0:0: [sda] Assuming drive cache: write through

# 通过 -T 参数能够看到具体的发生时间
$ sudo dmesg --level=err -T
[Tue Feb  2 05:16:33 2021] piix4_smbus 0000:00:07.3: SMBus Host Controller not enabled!
[Tue Feb  2 05:16:33 2021] sd 2:0:0:0: [sda] Assuming drive cache: write through

```

我们还可以通过搜索关键字的方式，只查看某一类组件的启动信息。如下代码分别显示内容、硬盘、USB以及TTY相关的启动信息。

```sh
$ dmesg | grep -i memory
$ dmesg | grep -i dma
$ dmesg | grep -i usb
$ dmesg | grep -i tty
```

还可以按照另外一个维度，分别输出特定类型的信息，包括：daemon、kern、user、mail、auth、syslog等。

```sh
$ dmesg --facility=kern
[    0.000000] Linux version 5.9.0-kali1-amd64 (devel@kali.org) (gcc-10 (Debian 10.2.0-15) 10.2.0, GNU ld (GNU Binutils for Debian) 2.35.1) #1 SMP Debian 5.9.1-1kali
2 (2020-10-29)
[    0.000000] Command line: BOOT_IMAGE=/vmlinuz-5.9.0-kali1-amd64 root=/dev/mapper/qking--vg-root ro quiet splash
[    0.000000] Disabled fast string operations
[    0.000000] x86/fpu: Supporting XSAVE feature 0x001: 'x87 floating point registers'
[    0.000000] x86/fpu: Supporting XSAVE feature 0x002: 'SSE registers'
[    0.000000] x86/fpu: Supporting XSAVE feature 0x004: 'AVX registers'
...
```

熟练使用 dmesg 命令，能够让我们在日常运维及更深层次的内核调试中了解更多的信息，上面总结的内容希望能够帮助到各位。

## 参考资料

1. [dmesg七种用法](https://www.cnblogs.com/zhaoxuguang/p/7810651.html)
2. [Linux dmesg命令](https://www.runoob.com/linux/linux-comm-dmesg.html)
3. [System hangs when a target reset is issued by hpsa](https://forums.centos.org/viewtopic.php?t=53899)