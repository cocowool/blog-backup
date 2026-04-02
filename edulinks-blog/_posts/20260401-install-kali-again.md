---
title: 再次安装 Kali
date: 2026-04-01 20:09:17
keywords: Kali, CTF, 大模型, Parallels Tools
description: 最近又开始倒腾环境，重新安装了 Kali，并且成功安装了 Parallels Tools，这样粘贴和界面都方便很多。
---

## Kali 更换需求

上次打 CTF 的时候，安装了一个 2025 某个版本的 Kali Everything ，花了好多时间，结果 Parallels Tools 死活装不上去。查了一下，可能是我的 Parallels Desktop 17 版本太低了，和 Kali 2025 相关的版本不匹配。

Kali Everything 装好之后，占用空间要 40G 以上了，实际很多功能也用不到。最近又在培训 CTF，有点时间重新安装一个 Kali 吧，这次用 Qwen 分析了下，Kali 2022.3 应该和当前的 PD 匹配，能够正常安装 Parallels Tools。

Pralallels Tools 安装了之后，可以和宿主机共享粘贴板，能够方便的拖拽文件，共享文件系统，对于图形展示和窗口缩放的支持也更好，对于日常操作效率的提升和体验有很大的帮助。

## Kali 安装的小问题

介绍下我本地的环境，操作系统是 macOS Monterey 12.2.1，Parallels Desktop 版本是 17.1.7-51588。

通过大模型 Qwen、Deepseek、Doubao 的辅助，初步确定安装 Kali 2022.3 版本，并且安装 Parallels Tools，这是我的目标。

第一次在安装 Kali 的时候就出问题，选择 Graphic Install 模式安装后，界面重复的提示 Cannot find display，无法进入安装的界面。

找到了一个解决办法，在提示选择安装模式的界面，按 Tab 键，会出现一个命令行，在后面添加

> nomodeset xdriver=vesa

![image-20260401201449851](20260401-install-kali-again/image-20260401201449851.png)

这次学会了，软件安装都先选默认的，后面需要了再弄别的。

完成之后装 Parallels Tools，第一步执行 `sudo apt update` 就出问题了，查了下发现还存在 2025年 Kali 不慎丢失密钥的问题，看来世界之大真是无奇不有啊。

> 个人感觉，学习一门编程，用在倒腾环境和工具的时间，估计能占学习过程的 20～30%。但是对于 CTF 来说，倒腾环境和工具的时间占比在 60～70%。

## Parallels Tools 安装的坑

![image-20260402081858958](20260401-install-kali-again/image-20260402081858958.png)

通过在线已经不太好装相关包了，一方面国内源没保留这么久的历史文件，官方源可能也没有了。

http://old.kali.org/kali/pool/main/l/linux/

```sh
$ sudo dpkg -i linux-kbuild-5.18_5.18.5-1kali6_amd64.deb
$ sudo dpkg -i linux-headers-5.18.0-kali5-common_5.18.5-1kali6_all.deb
$ sudo dpkg -i linux-headers-5.18.0-kali5-arm64_5.18.5-1kali6_amd64.deb
# 这个包安装提示没有 linux-compiler-gcc-11-x86
```

> 如果是 M 芯片的笔记本，需要选择 arm64 的包。我的笔记本是2019年最后一代 Intel 的 MBP 16

![image-20260402085936533](20260401-install-kali-again/image-20260402085936533.png)

![image-20260402095106081](20260401-install-kali-again/image-20260402095106081.png)

第一次登陆进去的时候，右上角会有一个 Parallels Tools 的提示，提示需要登出会话，下次进来之后在配置界面中选择的共享给 Kali 的文件夹会被挂在本地。

```sh
┌──(shiqiang㉿kali)-[/media/psf/Home]
└─$ ls -lha
total 8.0K
drwx------ 1 shiqiang shiqiang  448 Apr  1 14:16 Desktop
drwx------ 1 shiqiang shiqiang 1.2K Apr  2 10:54 Downloads
```

## 一点思考

## 参考资料

1. https://blog.csdn.net/weixin_47610939/article/details/127647055
2. https://forum.parallels.com/threads/patch-support-for-kernel-5-18-parallel-tools-17-1-4-51567.357611/