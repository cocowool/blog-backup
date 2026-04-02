---
title: 再次安装 Kali
date: 2026-04-01 20:09:17
keywords: Kali, CTF, 大模型, Parallels Tools
description: 
---

## Kali 更换需求

上次打 CTF 的时候，安装了一个 2025 某个版本的 Kali Everything ，花了好多时间，结果 Parallels Tools 死活装不上去。查了一下，可能是我的 Parallels Desktop 17 版本太低了，和 Kali 2025 相关的版本不匹配。

Kali Everything 装好之后，占用空间要 40G 以上了，实际很多功能也用不到。最近又在培训 CTF，有点时间重新安装一个 Kali 吧，这次用 Qwen 分析了下，Kali 2022.3 应该和当前的 PD 匹配，能够正常安装 Parallels Tools。

Pralallels Tools 安装了之后，可以和宿主机共享粘贴板，能够方便的拖拽文件，共享文件系统，对于图形展示和窗口缩放的支持也更好，对于日常操作效率的提升和体验有很大的帮助。

在提示选择安装模式的界面，按 Tab 键，会出现一个命令行，在后面添加
> nomodeset xdriver=vesa

![image-20260401201449851](20260401-install-kali-again/image-20260401201449851.png)

这次学会了，软件安装都先选默认的，后面需要了再弄别的。


完成之后装 Parallels Tools，第一步执行 `sudo apt update` 就出问题了，查了下发现还存在 2025年 Kali 不慎丢失密钥的问题，看来世界之大真是无奇不有啊。

> 个人感觉，学习一门编程，用在倒腾环境和工具的时间，估计能占学习过程的 20～30%。但是对于 CTF 来说，倒腾环境和工具的时间占比在 60～70%。
> 

