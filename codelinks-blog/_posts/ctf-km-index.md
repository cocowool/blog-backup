---
title: CTF解题知识体系汇总
date: 2023-05-07 14:08:39
keywords: ctf php, ctf 知识体系
description: 总结 CTF 比赛中用到的知识体系。 
---

本文整理 CTF 过程中所需要掌握的知识和分析方法，可能以后会整理一个思维导图，目前就使用 Markdown 格式分类展示。

## Web类

* URL编码规则，百分号开头，后面跟2位十六进制的ASCII码
* HTTP 协议基础，主要是四部分请求行、请求头、空行、请求数据
* 命令执行漏洞
  * 过滤绕过

* 文件上传漏洞
  * 一句话木马（小马和大马）

* 文件上传绕过
  * 前段校验绕过
  * MIME检查绕过
  * 文件头校验绕过
  * 服务端文件扩展名校验绕过

* 文件包含漏洞
  * 本地文件包含
  * 远程文件包含
  * filter 伪协议 / input 伪协议 / data 伪协议 / zip伪协议

## Java类

* jar 包反编译 jd-gui
* jar 包中 class 更新，`jar -uvf xx.jar xx.class`
* IDEA 编辑器使用 

## 编码类

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

* NFS流隐写，压缩包中可能有多个文件
* 图片中隐写文件或信息
* 图片lsb隐写，提取不同颜色位获取信息
* SNOW隐写

## 流量分析

* Wireshark 技巧
  * 使用统计功能总体上查看协议分布情况
  * 使用 http 或 tcp 快速查看总览情况
  * 使用 `http contains "flag"` 排查是否有敏感字段

## 音频类

* SSTV 慢扫描电视 Slow Scan Television

## PWN类

* 汇编基础
* ida 静态分析
* gdb 动态分析
* nasm 汇编编译器

## SQL注入

* 联合查询注入 select 1,2 union select name, age from table_name;
* 关键字过滤绕过 waf
  * select 过滤绕过，如 seleselectct
  * 空格过滤绕过，如 /**/ 或 %0a
  * 等号绕过，使用不加通配符的 like/

## 逆向

* 汇编基础
* 静态分析
* 动态分析

## 渗透

* 信息收集，nmap -T4 -v ip


## 参考资料

