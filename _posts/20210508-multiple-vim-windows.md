---
title: Vim的多窗口操作与比较功能介绍
date: 2021-05-08 13:36:11
tags:
keywords: vim, vim多窗口, vimdiff, linux
description: vim进行多窗口操作和文件比较的介绍。
---



## VIM的多窗口操作

在编辑多个文件时，如果能够多窗口操作将会带来很大的便利。这里的窗口并不是新开了一个Window，而是在一个Window中，利用Vim的分割窗口命令，同时编辑多个文件。

> 水平分割窗口     :split [filename]     可以分割出一个新的窗口
> 垂直分割窗口     :vertical split [filename]          可以分割出一个垂直的新窗口

在窗口内，我们通过 hkjl 可以在窗口中移动光标。

如果需要在窗口之间进行切换，使用 Control + w + [hkjl] 就可以了。

## VIM的比较功能

Vim也提供了很强大的比较功能 vimdiff 命令。

使用 vimdiff file1 file2 命令，可以打开vim窗口并对这两个文件进行内容的对比。
如果已经用vim打开编辑一个文件，也可以通过 :vertical diffsplit file2 来进行文件的对比。

对于文件中相同的部分，vim会进行折叠来方便我们查看只是发生修改的部分，如果需要展开折叠，可以用方向键、l、zo命令来打开折叠，如果需要关闭折叠，可以使用zc命令。

如果有一个文件的补丁，可以使用 vertical diffpatch file 命令来查看不同。

## VIM中的折叠

在Vim中启用折叠有很多种方法：

折叠相关的命令：zf、zo、zc、zd

     zc     打开折叠
     zC     对所在范围的所有折叠进行打开
     zo     关闭折叠
     zO     对所在范围的所有折叠进行关闭
     [z     到当前打开的折叠的开始处
     ]z     到当前打开的折叠的末尾处
     zj     向下移动。到达下一个折叠的开始处。关闭的折叠也被计入
     zk    向上移动到前一折叠的结束处。关闭的折叠也被
     zd    删除 (delete) 在光标下的折叠。仅当 ‘foldmethod’ 设为 “manual” 或 “marker” 时有效
     zD    循环删除 (Delete) 光标下的折叠，即嵌套删除折叠。仅当 ‘foldmethod’ 设为 “manual” 或 “marker” 时有效
     zE     除去 (Eliminate) 窗口里“所有”的折叠。仅当 ‘foldmethod’ 设为 “manual” 或 “marker” 时有效

快速创建折叠的方法

     zf56G     创建从当前行到56行的折叠
     10zf、10zf+、10zf下箭头，创建从当前行到向下10行处的折叠
     10zf-、10zf上箭头，创建从当前行到向上10行处的折叠
     在括号处，使用 zf% 创建从当前行到对应括号处的折叠

快捷键
```
.                       可以执行上一次的VIM命令
n<command>     					可以重复执行某个命令N次
nG                      将光标移动到某一行
*                       向下寻找同一个单词
#                       向上寻找同一个单词
```

对于vim有更多兴趣的朋友，欢迎看看其他vim相关文章。

1. [VIM快捷键入门与精通](http://www.edulinks.cn/2021/04/01/20210401-vim-shortcut/)
2. [我的vim配置](http://www.edulinks.cn/2021/03/26/20210329-my-vimrc/)
3. [VIM在多窗口编辑时的几个快捷键](http://www.edulinks.cn/2010/06/25/20100625-vim-split/)
4. [VIM的常用操作](http://www.edulinks.cn/2009/04/15/20090415-vim-operation/)
5. [VIM的使用技巧](http://www.edulinks.cn/2009/01/19/20090119-vim-tips/)

