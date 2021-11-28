---
title: 基于 Node.js 写一个命令行工具
keywords: '命令行工具, Node.js'
description: 本文介绍基于 Node.js 写一个命令行工具。
date: 2021-11-28 22:12:27
tags:
---

虽然现在图形化开发工具发展的已经非常成熟，但我们日常的开发工作和生产支持中还是离不开命令行工具。毕竟服务器上不会安装图形界面，本地开发使用命令行工具也能够大大提高开发效率，例如 `git` 、`npm` 等等。

Node.js 是一个基于 Chrome V8 引擎的 JavaScript 运行环境。Node.js 使用事件驱动、非阻塞的 I/O 模型，使其具备轻量高效的特点。npm 目前是全球最大的 Node.js 生态系统开源仓库。

本文目标是创建一个基于 Node.js 的命令行工具，将本地的 Markdown 文件转换为 html 文件，并保存到指定的目录，这个工具的名字叫做 **[popsite](https://github.com/cocowool/popsite)**。计划的使用方法

```sh
$ pop markdown_file.md # 将当前文件夹下的 markdown_file.md 文件转换为 markdown_file.html 文件
```

 ## 初始化工程

```sh
$ mkdir popsite
$ cd popsite
$ npm init			# 初始化之后根据需要填写相关的内容，后续可以在 package.json 中进行修改
```

## 生效命令

我们可以使用 `npm link` 将当前的模块发布到全局的环境中，方便在本地进行测试。

```sh
➜  ~ pwd
/Users/shiqiang
➜  ~ pop
Pop init in ./bin/pop.js
➜  ~ npx pop
Pop init in ./bin/pop.js
```

## 处理参数

建议使用 commander 来提速参数的处理、帮助文档的展示、版本显示等功能的开发。

```javascript
    program
        .version(require('../package.json').version)
        .usage('pop markdown_file')
        .parse(process.argv)
```

## 编写业务逻辑

我将主要负责业务逻辑的模块放在了 `lib/popsite` 文件夹下，看了几天 hexo 的源码，感觉其基本思路也是将不同的功能模块，存放在 lib 下的不同文件夹中，通过一个模块调用的方法来根据模块名称实现相关功能的动态调用。

下面是刚打好框架的源码内容：

```javascript
'use strict'

const fs = require('fs')

var Popsite = {
    markdown2html : function( file_name ){

        console.log("Begin Markdown to html, filename : " + file_name)
        markdown_content = fs.readFile(file_name)
        console.log(markdown_content)
    }
}

module.exports = Popsite;
```

## 发布到npm

发布到 npm 可以让我们的工具更方便被更多的人使用。在 [npmjs](https://www.npmjs.com) 官网可以注册账号，发布的过程可以在命令行中完成，只需要下面一个命令就可以完成。

```sh
$ cd popsite
$ npm publish
```



## 相关资料

1. [通过npm写一个cli命令行工具](https://www.cnblogs.com/buzhiqianduan/p/7655612.html)
2. [How to build a CLI with Node.js](https://www.twilio.com/blog/how-to-build-a-cli-with-node-js)
3. [How to Build a Command-line Utility (CLI) with Node.js](https://hackernoon.com/how-to-build-a-command-line-utility-cli-with-nodejs-gm24315b)
4. [Hexo 源码解析](https://blog.csdn.net/li20081006/article/details/73319054)
5. [5分钟带你读懂Hexo源码设计模式](https://www.jianshu.com/p/ef88b5bbb914)
5. [node.js如何制作命令行工具](https://www.cnblogs.com/wmhuang/p/7052587.html)
5. [用 nodejs 写命令行工具](https://blog.csdn.net/henryhu712/article/details/84262290)
5. [Node.js+commander开发命令行工具](https://www.jianshu.com/p/2cae952250d1)
5. [NodeJS获取当前目录和运行文件所在目录](https://blog.csdn.net/mouday/article/details/105325635?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-1-105325635.pc_agg_new_rank&utm_term=nodejs获取程序运行目录&spm=1000.2123.3001.4430)
