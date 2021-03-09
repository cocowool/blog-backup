---
title: 我的 zsh 配置
keywords: 'zsh, shell, linux shell, linux zsh'
description: >-
  zsh 号称「终极shell」，在我的 [Catalina
  默认使用zsh了，你可习惯](https://www.edulinks.cn/2020/07/18/20200718-zsh-and-bash/)
  文章中也介绍了zsh的一些基本特点，但光了解这些基本的还是没办法提高生产力的，本文用来介绍如何配置 zsh 让你的工作效率飞起来。
date: 2021-03-09 16:11:53
tags:
---


zsh 号称「终极shell」，在我的 [Catalina 默认使用zsh了，你可习惯](https://www.edulinks.cn/2020/07/18/20200718-zsh-and-bash/) 文章中也介绍了zsh的一些基本特点，但光了解这些基本的还是没办法提高生产力的，本文用来介绍如何配置 zsh 让你的工作效率飞起来。

我的需求：

* History 支持的记录数尽可能的多，因为经常容易忘记敲过的命令
* 在所有的终端窗口间共享历史命令
* 最好能够彩色显示不同的文件夹和不同类型的文件，这样看起来有点酷

## .zshrc

先把我的配置文件分享出来

```sh
# 设置zsh的家目录
export ZSH=$HOME/.oh-my-zsh

# 设置使用的主题
ZSH_THEME='robbyrussell'

# 打开自动完成功能
autoload -U compinit && compinit

# 开启自动切换目录功能
setopt auto_cd

# 消除重复的历史命令
setopt HIST_IGNORE_DUPS

source $ZSH/oh-my-zsh.sh

# ls 别名，默认显示详细信息
# alias ls='ls -lha'
alias ll='ls -lha'
alias grep='grep --color=auto'
```

## oh-my-zsh

**Oh My Zsh** 是一个帮助我们快速配置 zsh 的开源程序，[项目地址](https://github.com/ohmyzsh/ohmyzsh) 可以下载最新的源码。提供了很多有用的插件以及众多的样式，我选择了手工安装的方式，从项目主页上下载代码包，解压后拷贝到 `~/.oh-my-zsh` 目录，然后按照我上面的配置文件，就可以使用 oh-my-zsh 了。

## 日常使用

* 查找历史命令 `Ctrl + r` 是最常用的，输入几个字母就能联想出敲过的命令
* Git 插件提速git操作，例如 `git add --all` 可以缩写成 `gaa`，所有的命令在 `cat ~/.oh-my-zsh/plugins/git/git.plugin.zsh` 中。这个插件是默认开启的。
* **autojump** 插件能够实现目录间的快速跳转，不仅仅是省略 `cd` 这么简单，还能够记住很多目录的名字，这样我们通过 `j folder-name` 这样的方式，就可以直接进入相关的目录，不用再关心前面的路径，使用起来非常方便。在Mac下需要先通过 `brew install autojump` 安装后才能启用该插件。
* **zsh-syntax-highlighting** 插件能够对错误的shell命令给出颜色警告。安装方法 `git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ~/.oh-my-zsh/custom/plugins/zsh-syntax-highlighting` 然后参照我之前的配置文件启用插件就行。
* **zsh-autosuggestions** 能够自动进行命令提示，使用 Tab 或者 右箭头就可以补全，对于重复敲的命令非常方便。安装方法 `git clone git://github.com/zsh-users/zsh-autosuggestions ~/.oh-my-zsh/custom/plugins/zsh-autosuggestions` 然后参照我之前的配置文件启用插件就行。

> 本文没有涉及如何切换默认Shell，因为Mac换到Catalina之后，已经将 zsh 设置为默认的 Shell 了。

## 参考资料

1. [zsh || bash 快捷键](https://www.jianshu.com/p/eeee90f27bcc)
2. [oh-my-zsh](https://github.com/ohmyzsh/ohmyzsh)
3. [终极 Shell](http://macshuo.com/?p=676)
4. [oh-my-zsh 插件](https://hufangyun.com/2017/zsh-plugin/)

