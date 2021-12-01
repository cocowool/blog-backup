---
title: 构建一个转换 Markdown 文件的 Node.js 小工具
date: 2021-11-30 22:51:49
keywords: Node.js, Markdown, Markdown to html
description: 

---

本文是上一篇文章 [基于 Node.js 写一个命令行工具](http://www.edulinks.cn/2021/11/28/20211128-npm-cli-module-develop/) 的续篇，在上一篇文章中我们了解到构建一个 Node.js 命令行工具的主要步骤。本篇在此基础上，完成我们主要的业务代码编写。

这个工具的主要功能是将本地的 Markdown 文件转换为 HTML 文件。

## markdown-it

markdown-it 是一个基于 Node.js 的 Markdown 文件解析器，拥有很好的活跃度，文章发布时的最新版本是 12.0.0 ，拥有每周200万的下载量。markdown-it 的使用也非常简单，创建 markdown-it 对象后，将需要转换的文件内容传递给 render 函数，就能够输出转换后的 html 内容。更详细的使用方式还请大家参考它的官网，本文不再多说。



## 参考资料

1. [markdown-it @ github](https://github.com/markdown-it/markdown-it#readme)
2. [Convert Markdown to HTML with Node.js](https://www.devextent.com/convert-markdown-to-html-nodejs/)
3. [使用markdown-it插件](https://blog.csdn.net/qq_45138936/article/details/105453817)
4. [markdown-it 的解析过程](https://www.jianshu.com/p/fb0ee355915c)
