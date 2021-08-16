---
title: Node.js 语言介绍
date: 2021-08-10 23:15:54
tags:
keywords: node.js, javascript
description: JavaScript从1995年出现之后，经历了快速的发展和应用，基本上成为前端编程语言的标准选择。Node.js 做为一个异步事件驱动的 JavaScript 运行时，Node.js 被设计用来构建可扩展的网络应用。
---

## JavaScript历史

1995年网景公司的Brendan Eich设计实现了JavaScript，最初名称为LiveWire，后来改名为LiveScript，其起因是因为当时网络很昂贵，为了避免将页面发送给服务端后才进行校验而开发了前端校验的编程语言，同时也为非专业的开发人员提供一个方便的工具。与此同时，Sun公司在推广Java applet，但是applet速度慢而且操作不便。趁此机会，网景公司与Sun合作完成了LiveScript的实现，并在Navigator 2.0发布之前，将其改名为JavaScript。1996年微软的Windows95推出了Internet Explorer3，支持JScript和VBScript。

由于JavaScript没有一个标准，在不同的浏览器之间存在兼容性问题。1996年，JavaScript标准由诸多厂商共同提交给ECMA（欧洲计算机制造商协会），ECMA通过了标准ECMA-262，也就是ECMAScript。紧接着国际化标准组织也采纳了这个标准ISO-16262。1999年12月，ECMAScript 3.0发布并获得了巨大的成功，在业界得到了广泛的支持，成为通行标准。2015年6月，ECMAScript 6正式发布，并且更名为「ECMAScript 2015」，即是现在的ES6。


## Node.js 介绍
Node.js是可以在服务端运行JavaScript的平台，具备单线程、异步式I/O、事件驱动式程序设计模型，这些带来了性能的提升，减少了多线程程序设计的复杂性，进而提高了开发效率。

2011年3月18日，Node v0.4.3版本发布。到2021年8月11日 Node v16.6.2版本发布，十年的时间Node.js得到了快速的发展。从百度指数上可以看到，Node.js从发布以来一直快速增长，到目前的热度基本上与JavaScript持平了。

![image-20210813083633062](20210812-node-program-introduce/image-20210813083633062.png)

写过JavaScript的同学可能清楚，在JavaScript语言中没有包、模块的概念，这有点阻碍JavaScript实现工程化的应用。为了统一JavaScript在浏览器之外的实现，CommonJS项目诞生了。它的终极目标是制定一个像C++标准库一样的规范，提供标准化的JavaScript模块单元，使得基于CommonJS 规范的应用可以在不同环境下运行。

Node.js 最初遵循CommonJS规范，这就使得使用Node.js来开发大型的后端应用成为可能，并且也能够很方便的转化为浏览器支持的代码（如Webpack等）。在Node.js v13.2.0版本之后，Node.js 默认打开了ES6 Module的支持。ES6（EcmaScript 6）可以较好的兼容后端与前端的开发需求，目前已经成为主要的标准。

## 模块机制
在Node.js中，每个文件就是一个模块，都有自己的作用域。在一个文件里面定义的变量、函数、类都是私有的，其他文件不可见。每个模块内部可以使用 `require` 来加载某个模块，`module` 表示当前模块，是一个保存了当前模块信息的对象。`exports` 是 `module` 上的一个属性，保存了当前模块要导出的借口或者变量。

分为两类：

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
3. [CommonJS：不是前端却革命了前端](https://zhuanlan.zhihu.com/p/113009496)

