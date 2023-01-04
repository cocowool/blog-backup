---
title: VIM的常用操作
date: 2009-04-15 17:35:01
tag: 
keywords: vim, vim操作, linux
description: Vim 作为 Linux 开发运维的必不可少的工具，如果能够将 Vim 的功效发挥到极致，必然能够提高我们的工作效率，本文介绍了一些 Vim 的使用技巧。
---

> 工欲善其事，必先利其器

Vim 作为 Linux 开发运维的必不可少的工具，如果能够将 Vim 的功效发挥到极致，必然能够提高我们的工作效率，下面是一些 Vim 的使用技巧。

## 光标定位、翻屏滚屏。

| 命令 | 解释                           |
| ---- | ------------------------------ |
| $    | 将光标移动到当前行的末尾       |
| 0    | 将光标移动到当前行的开头       |
| gg   | 将光标移动到文件的开头         |
| G    | 将光标移动到文件的末尾         |
| 10gg | 将光标跳转到固定行（如第10行） |
| H    | 将光标跳转到屏幕上的第一行     |
| M    | 将光标跳到屏幕的中间行         |
| L    | 将光标跳到屏幕的最后一行       |
| gk   | 将光标跳到屏幕所见的上一行     |
| gj   | 将光标跳到屏幕所见的下一行     |
|      |                                |

在Vim的命令模式下，有很多移动光标的命令，带给我们很大的便利。最常用的莫过于和，把光标定位在行头和行尾。

常用的还有把光标定位在文件尾，回到文件头。在这两个命令前面加一个数字就可以去到那一行，例如会带你去到第10行。

如果你发觉有时候和不是很好用（按一下就跨越屏幕上的几行），可以试一下用和。

## 复制、剪切、粘贴操作

| 命令   | 解释                       |
| ------ | -------------------------- |
| dd     | 删除当前行                 |
| yy     | 复制当前行                 |
| d$     | 删除从光标位置到行尾的内容 |
| `:reg` | 查看各个寄存器的内容       |
| D      | 删除光标处至行尾的字符     |