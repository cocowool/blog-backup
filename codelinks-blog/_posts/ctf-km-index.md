---
title: CTF解题知识体系汇总
date: 2023-05-07 14:08:39
keywords: ctf php, ctf 知识体系
description: 总结 CTF 比赛中用到的知识体系。 
---

## 编码

* base64 解码
* HTML编码，`&#x63;`

## 加解密

* binwalk，先过滤下文件
* 010editor 查看下文件格式
* zip 加密使用 ARCHPR 暴力破解，优先考虑纯数字、纯字母
* 仿射密码 Affine Cipher，[在线解密工具](http://www.hiencode.com/affine.html)
* Zip文件格式，判断是否假加密。Zip 文件以 50 4B 区分各区
* 栅栏密码
* 莫尔斯电码

## 图片类

* 图片中隐写文件或信息
* 图片lsb隐写，提取不同颜色位获取信息

## 音频类

* SSTV 慢扫描电视 Slow Scan Television

## PWN题目

* 汇编基础
* ida 静态分析
* gdb 动态分析
* nasm 汇编编译器

## 参考资料

