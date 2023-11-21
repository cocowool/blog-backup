---
title: Springboot 工程的配置集成
date: 2023-11-21 08:28:03
keywords: springboot, properties, yml, springboot 配置
description: 本文介绍 Springboot 工程自定义配置文件的使用方法，记录相关学习心得体会。
---

Springboot 支持 properties 或者 yml 两种形式的配置文件，配置文件有四个可以保存的位置：
* 项目的根目录下
* 项目根目录下的 config 文件夹
* 项目 src/main/resources 目录
* 项目 src/main/resources/config 目录

## 区分测试和生产环境

Profile 的管理，配置文件指定，命令行指定

## 参考资料
1. [Springboot 配置文件格式](https://baijiahao.baidu.com/s?id=1766310050451174842&wfr=spider&for=pc)
2. Springboot 整合开发实战
3. [springboot基础系列-properties配置](https://www.cnblogs.com/V1haoge/p/7183408.html)