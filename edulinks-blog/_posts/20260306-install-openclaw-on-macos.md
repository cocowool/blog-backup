---
title: 从上门安装到官方卸载，试论OpenClaw的全民热潮
date: 2026-03-06 08:38:30
keywords:
description: 记录一次OpenClaw学习使用的过程，实现一个简单的案例，实现与在线大模型和在线通知工具的集成。
---

最近 OpenClaw 真的太火了，连我们平时不怎么谈技术的大领导在工作交流时，也要求我们去学习学习 OpenClaw，于是我开始连夜看起了 OpenClaw 相关的技术资料、博客和各种短视频。

我在本地的 macOS 环境中也搭建了一个 2026.3.2 版本的 OpenClaw，配置了一个 Agent，对接了 qwen-portal 模型，简单体验了下 OpenClaw 的相关功能。

![image-20260316101603247](20260306-install-openclaw-on-macos/image-20260316101603247.png)

OpenClaw 的安装确实比较简单，只要依赖的基础环境（Node、Python）都具备的话，只需要一条命令行就能够安装。安装完成后，也提供了快速启动的命令（通过`openclaw`），很快就能在网页端体验。我这个版本的 OpenClaw  默认了提供了 51个Skills，每个 Skills 依赖不同的 Tools 权限。

Tools 是和 Agent 绑定的，默认的 Agent 提供了几类 Tools，包括：
* Files 文件类：可以操作 OpenClaw 所在机器的本地文件；
* Runtime 命令执行类：可以操作 OpenClaw 所在机器的命令行，执行相关命令；
* Web 网站搜索和网页获取；
* Memory 是 OpenClaw 提供的对话记录工具。我们通过 Web 界面使用大模型，在不同会话之间的记录不能共享，有了这个系统可讲我们与大模型交互的对话历史保留下来。
* Session 是 OpenClaw 的记忆容器，有三种类型：会话Session、执行Session、子代理Session。


## 环境与安装

本地的环境
* macOS Monterey
* Python 3.12.5
* Node v24.13.0
* OpenClaw 2026.3.2
* DeepSeek 开发者平台申请密钥

我本地原来 Node 版本比较低，在安装 OpenClaw 的命令中支持升级 Node，但是不知道为什么找不到编译好的版本，走到了下载源码编译安装的分支，导致整个过程很慢多次出错，后来我选择了去 Node 官网下载安装包单独安装升级。升级 Node 版本之后，再运行安装 OpenClaw 的命令，此时执行很快，没有再报错。

```sh
$ curl -fsSL https://openclaw.ai/install.sh | bash
$ openclaw -v    
2026.3.2
```

##  场景

> 在本地应用之前，看了网上的一些教程，首先提醒的是注意数据安全，尤其是自己的密钥、个人文档之类的材料。在OpenClaw运行模式选择上，为了安全肯定使用容器方式运行更安全受控。如果是本地运行，注意进行相关代码的审核。

我想实现一个通过聊天工具（如钉钉），联动大模型解答问题，提供天气查询服务的简单场景。


![Openclaw网页对话界面](20260306-install-openclaw-on-macos/image-20260309110203859.png)

## 有用的配置



## 思考

真正有用的 Tools 或者 Siklls，还是需要企业内部根据自己的实际需要去开发。

## 参考资料

1. https://www.cnblogs.com/xiaobaiysf/p/19595515
2. https://zhuanlan.zhihu.com/p/2012240792371098520
3. https://docs.openclaw.ai/zh-CN