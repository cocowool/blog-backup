---
title: ARCHPR 介绍
date: 2024-01-16 17:08:15
keywords: xray, ctf archpr, ctf tools, ctf 工具合集, ctf 工具介绍
description: ARCHPR 是一款密码恢复软件，支持多种密码找回方式。
category: CTF
---


[ARCHPR](https://us.elcomsoft.com/archpr.html) 是 Elcomsoft 开发的一款压缩包密恢复工具，支持广泛的压缩包类型和加密算法。

![ARCHPR. Guaranteed WinZip Recovery](ctf-tools-archpr/archpr_s3.png)

ARCHPR 支持四种破解类型：暴力、字典、明文、掩码。

## 暴力 Brute-force

暴力破解方式是大家最容易想到使用的方式，从纯数字、纯字母等范围中，穷举并尝试破解密码。对于单独的类型效率比较高，如果是既有数字、字母大小写、特殊字符，位数在8位以上的，破解时间非常久。

## 字典 

字典破解方式是使用从网上收集的常用密码字典尝试破解，有些人也可能会收集到之前各类安全事件中泄漏出来的用户真实密码。

## 掩码

如果有办法获取到密码中的一部分内容，例如知道某一位是一个数字，则可以使用掩码模式。

## 明文

明文破解方式是在你知道压缩包中的部分文件内容或部分文件时，可以利用已知文件来寻找加密密钥。

## 参考资料

1. [Advanced Archive Password Recovery](https://us.elcomsoft.com/archpr.html)