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

zsh 号称是「终极shell」，从这个称号看出来它的功能应该很强大。

## Fish Shell

### 参考资料

1. [在 Mac 上将 zsh 用作默认 Shell](https://support.apple.com/zh-cn/HT208050)
2. [zsh取代bash 成为macOS Catalina的默认shell](https://www.cnbeta.com/articles/tech/853837.htm)
3. [Fish shell 入门教程](http://www.ruanyifeng.com/blog/2017/05/fish_shell.html)
4. [百度百科 shell](https://baike.baidu.com/item/shell/99702?fr=aladdin)

