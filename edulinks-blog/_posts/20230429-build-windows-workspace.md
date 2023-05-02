---
title: 构建Windows下的工作环境
date: 2023-04-29 9:22:23
keywords: Windows, Windows 环境, Windows CTF
description: 办公电脑对于IT从业者来说非常重要，最近需要准备一个 Windows 下的环境，本文介绍如何准备 Windows 环境，用于满足日常写作、CTF、代码开发相关工作。
---

## Windows
制作启动U盘，这里采用了老毛桃提供的工具。不太好的是，通过老毛桃启动盘安装的 Windows 会自带一些360、驱动人生之类的软件，如果不需要可以后续卸载，目前看也没有什么问题。

关于 Windows 版本，之前用过一段时间 Windows 10 企业版，网上的反馈是系统比较精简。实际体验确实比较精简，连 Microsoft Store 也没有，还需要自己安装。安装过程中可能会有一些奇怪的报错。

这次为了方便，我直接选择了专业版，版本号是 22H2，系统安装好之后 Microsoft Store 和一些个性化配置的软件都有了，全家桶多占用了一些空间，带来了不少方便。

## 办公
* 微信，已经是事实上的电脑必备软件
* Typora，在荔枝中国上购买了软件许可，支持激活三台设备，这次正好用上
* 印象笔记
* 百度网盘，很多资料分享都是通过百度网盘，安装一个比较方便。版本：BaiduNetdisk_7.26.0.10。

## CTF
* BurpSuite
* Sqlmap
* Vmware Workstation 17 Player，为了安装 Kali 用的，这个版本允许用于非商业用途
* Kali，我选择从官方下载 ISO 后自己安装

## 开发
* WSL
* Ubuntu，通过微软商店安装的，可以通过 /mnt/ 目录访问 Windows 中的文件
* Visual Studio Code，版本 
* Python环境：Anacoda
* Github，如果需要提交代码，记得生成一个专用的 Token

## 参考资料
1. [WSL安装和迁移](https://wap.sciencenet.cn/blog-3474055-1341445.html?mobile=1)
2. [Win10下Ubuntu(WSL)中Python环境配置笔记](https://zhuanlan.zhihu.com/p/63897033)