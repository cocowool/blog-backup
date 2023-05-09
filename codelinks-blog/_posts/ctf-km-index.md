---
title: CTF解题知识体系汇总
date: 2023-05-07 14:08:39
keywords: ctf php, ctf 知识体系
description: 总结 CTF 比赛中用到的知识体系。 
---

## 编码类

* base64 解码
* HTML编码，`&#x63;`

## 加解密

* 仿射密码 Affine Cipher，[在线解密工具](http://www.hiencode.com/affine.html)
* Zip文件格式，判断是否假加密。Zip 文件以 50 4B 区分各区

## 图片类

* NFS流隐写，压缩包中可能有多个文件
* 图片中隐写文件或信息
* 图片lsb隐写，提取不同颜色位获取信息
* SNOW隐写

## 流量分析

* Wireshark 技巧
  * 使用统计功能总体上查看协议分布情况
  * 使用 http 或 tcp 快速查看总览情况
  * 使用 `http contains "flag"` 排查是否有敏感字段

## 参考资料

