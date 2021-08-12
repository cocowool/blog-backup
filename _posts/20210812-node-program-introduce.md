---
title: Node.js 语言介绍
date: 2021-08-10 23:15:54
tags:
keywords: node.js, javascript
description: 
---

## Node.js 介绍
Node.js是可以在服务端运行Javascript的平台，具备单线程、异步式I/O、事件驱动式程序设计模型，这些带来了性能的提升，减少了多线程程序设计的复杂性，进而提高了开发效率。

Javascript是由ECMAScript、文档对象模型（DOM）、浏览器对象模型（BOM）组成。

## Javascript历史

1995年网景公司的Brendan Eich设计实现了Javascript，最初名称为LiveWire，后来改名为LiveScript，其目标是为非专业的开发人员提供一个方便的工具。与此同时，Sun公司在推广Java applet，但是applet速度慢而且操作不便。趁此机会，网景公司与Sun合作完成了LiveScript的实现，并在Navigator 2.0发布之前，将其改名为JavaScript。

1996年微软的Windows95推出了Internet Explorer3，支持JScript和VBScript。

由于JavaScript没有一个标准，在不同的浏览器之间存在兼容性问题。1996年，JavaScript标准由诸多厂商共同提交给ECMA（欧洲计算机制造商协会），ECMA通过了标准ECMA-262，也就是ECMAScript。紧接着国际化标准组织也采纳了这个标准ISO-16262。

为了统一JavaScript在浏览器之外的实现，CommonJS诞生了。它的终极目标是制定一个像C++标准库一样的规范，使得基于CommonJS API的应用可以在不同环境下运行。

## 两个规范
- EcmaScript，Javascript的浏览器端规范
- CommonJS，Javascript的服务器端规范

## 模块加载机制
Node.js的模块分为两类：
* 核心模块：Node.js标准API中提供的模块，如fs、http、net、vm等，编译成了二进制代码，可以直接通过`require`获取
* 文件模块：存储为单独的文件（或文件夹）的模块，可能是js代码、json代码或编译好的C/C++代码（后缀为`.node`）

文件模块的加载方式有两种：
* 按路径加载，如果`require`参数以`/`开头，就以绝对路径的方式查找模块名称，并依次尝试加载`.js`、`.json`、`.node`后缀
* 查找 `node_modules` 文件夹，首先查找当前目录，如果没有就依次向上级目录查找，直到根目录

### node_modules

Node中的依赖，如果项目中不写package.json，那么依赖的就是全局的库。如果写了 package.json，就会把所有依赖下载到 node_modules 文件夹。

## 语法

1. 模块和包
Module 模块和 Package 包是两个重要概念，模块和文件是一一对应的，一个Node.js文件就是一个模块。包是一个目录，其中包含一个JSON格式的包说明文件 package.json 。
2. 全局对象
Global Object，它及其所有属性都可以在程序的任何地方访问，即全局变量。console、process是全局变量。
3. 常用工具 util
- util.inherits 实现对象间原型继承
- util.inspect 将任意对象转换为字符串
- util.isArray()
- util.isRegExp()
- util.isDate()
- util.isError()
4. 事件驱动 events
events.EventEmitter 是事件发射与事件监听器功能的封装
5. 文件系统 fs
fs是文件操作的封装，支持文件的读取、写入、重命名、删除、遍历目录、建立链接等操作，并且都提供了同步和异步两种接口。
6. http 模块


## 调试方法
单步调试
```sh
$ node debug debug.js
```

## NPM

Npm是基于Node.js的Javascript包管理工具，全称为 Node Package Manager。


### 参考资料
1. [Node.js 开发指南 BYVoid编著]()
2. [npm中package.json详解](https://www.cnblogs.com/zourong/p/5943224.html)