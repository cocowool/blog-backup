---
title: Nginx Rewrite 使用总结
tags:
---

Rewrite是网站建设中经常用到的一项技巧，通过rewrite我们能够屏蔽服务器运行态的信息，包括服务的程序、参数等等，给用户呈现美化后的URL，同时对搜索引擎更加友好，方便我们网站的推广。本文介绍了如何配置使用nginx rewrite功能。

<!-- more -->

[TOC]

## rewrite 功能介绍

Rewrite 是web服务器常用的一个功能，用来进行路径美化 / 转化时非常高效。



## nginx rewrite

Nginx提供了rewrite功能，结合正则表达式和标志位实现URL的重写与重定向,正则规则采用PCRE（PERL兼容的正则表达式语法规则）。

> 如果需要正则功能，在编译Nginx之前，需要编译安装PCRE库。

rewrite规则只能放在`server{}`,`location{}`,`if{}`中，并且只能对域名后边除去传递的参数外的字符串起作用。。

### rewrite 语法

rewrite 是实现URL重写的关键，根据正则表达式的内容，重新定向到replacement指定的URL，根据末尾的flag参数不同，决定后续的处理动作。

| 指令语法 | rewrite regex replacement [flag] |
| -------- | -------------------------------- |
| 默认值   | none                             |
| 应用位置 | server、location、if             |
|          |                                  |

### flag标记

| flag标记符号 | 说明                                                         |
| ------------ | ------------------------------------------------------------ |
| last         | 本条规则匹配完成后，继续向下匹配新的 location URI 规则，浏览器地址栏URL地址显示原来的地址 |
| break        | 本条规则匹配完成即终止，不再匹配后面的任何规则，浏览器地址栏URL地址显示原来的地址 |
| redirect     | 返回302临时重定向，浏览器地址栏会显示跳转后的URL地址         |
| permanent    | 返回301永久重定向，浏览器地址栏会显示跳转后的URL地址         |

在flag标记中，`last` 和 `break` 实现功能类似，使用 `alias` 指令时必须用 `last` 标记，使用 `proxy_pass` 指令时要使用 `break` 标记。`last` 标记在本条 rewrite 规则执行完毕后，会对其所在的 `server{…}` 标签重新发起请求，而 `break` 标记则会在本条规则匹配完成后，终止匹配，不再匹配后面的规则。

## 参考资料

1. [nginx中try_files](https://www.cnblogs.com/boundless-sky/p/9459775.html)
2. [Nginx rewrite or internal redirection cycle while internally redirecting](https://stackoverflow.com/questions/37214921/nginx-rewrite-or-internal-redirection-cycle-while-internally-redirecting)
3. [Nginx rewrite](https://blog.csdn.net/qq_41475058/article/details/89516051)
4. 