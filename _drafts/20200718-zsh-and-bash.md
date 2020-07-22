---
title: Catalina 默认使用zsh了，你可习惯
date: 2020-07-18 10:01:57
tags: Zsh
---

### zsh 成为默认 shell

淘汰掉我的旧MBP换新后，欢天喜地打开Terminal，感觉有点不对，提示符什么时候变成了 `%`。

![image-20200718223437348](20200718-zsh-and-bash/image-20200718223437348.png)

查询了一些资料发现，原来在2019年WWDC期间，苹果推出了macOS Catalina，并且将zsh设置为操作系统默认shell。因为之前我的笔记本都是从EI Captin一路升级上来的，所以没体会到shell的变化，最近新换了笔记本后，才发现默认shell的变化。

苹果在其官网上，也提供了如何更改默认 Shell 的[操作步骤](https://support.apple.com/zh-cn/HT208050)。

## bash

shell 俗称壳，是用来与 kernel 内核做区分，作用是给计算机使用者提供操作界面，与计算机内核进行交互。它接收用户命令，对命令做解析，然后调用系统中的应用。

shell 有很多种，这里介绍几个常见的shell。

**第一个 Unix Shel**l 是1979年底在V7 Unix（AT&T第7版）中引入的，以它的资助者 Stephen Bourne 命名。Bourne shell 是一个交互式命令解释器和命令变成语言。

**Bourne Again Shell** （bash）是GNU计划的一部分，用来替代 Bourne shell。现在大多数Linux发行版都适用bash作为默认的shell。

## zsh

zsh 号称是「终极shell」，从这个称号看出来它的功能应该很强大。zsh的历史也很悠久，保罗·弗斯塔德（Paul Falstad）于1990年在普林斯顿大学求学时编写了Zsh的初版。

zsh具有以下主要功能：

- 开箱即用、可编程的命令行补全功能可以帮助用户输入各种参数以及选项。
- 在用户启动的所有shell中共享命令历史。**这一点非常棒，曾经因为sh无法很好的解决多个窗口共享历史命令的问题头疼了一阵儿**。
- 通过扩展的文件通配符，可以不利用外部命令达到find命令一般展开文件名。
- 改进的变量与数组处理。
- 在缓冲区中编辑多行命令。
- 多种兼容模式，例如使用/bin/sh运行时可以伪装成Bourne shell。
- 可以定制呈现形式的提示符；包括在屏幕右端显示信息，并在键入长命令时自动隐藏。
- 可加载的模块，提供其他各种支持：完整的TCP与Unix域套接字控制，FTP客户端与扩充过的数学函数。
- 完全可定制化。

由此

## Fish Shell

> 在我整理资料的过程中，不断的看到一些文章资料介绍 `fish shell` 并且给它打上 `90后` `最现代` 等一系列标签来彰显它的年轻与现代化，出于好奇我也整理试用了一下。



### 参考资料

1. [在 Mac 上将 zsh 用作默认 Shell](https://support.apple.com/zh-cn/HT208050)
2. [zsh取代bash 成为macOS Catalina的默认shell](https://www.cnbeta.com/articles/tech/853837.htm)
3. [Fish shell 入门教程](http://www.ruanyifeng.com/blog/2017/05/fish_shell.html)
4. [百度百科 shell](https://baike.baidu.com/item/shell/99702?fr=aladdin)

