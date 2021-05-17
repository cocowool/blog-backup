---
title: bash 终端配置
date: 2021-05-17 12:05:31
tags:
keywords: bash, bash terminal, 终端, linux终端
description: 本文介绍bash环境配置的基础知识。
---

bash 是我们常用的脚本执行环境，在Linux下终端配置可以通过修改 .bashrc、.profile 等文件来修改。本文对 Mac 下bash环境配置进行了简单介绍。

> 自从macOS Catalina之后，Mac默认的终端已改为zsh，参考[我的zsh配置](http://www.edulinks.cn/2021/03/09/20210309-zsh-config/) 了解zsh的配置步骤。

## 一、配置前的准备

了解.profile、.bashrc等的区别。

### 1./etc/profile和用户主目录下的配置文件

用户登陆到shell时，shell会执行一系列初始化动作，其中就包括读取配置文件，然后根据配置文件设置环境信息。这样用户就可以很方便地在环境中加入自定义信息，如设置环境变量。事实上，登陆shell会读两配置个文件，/etc/profile文件和用户主目录下的配置文件，这两个文件的区别是：

/etc/profile文件：为系统的每个用户设置环境信息，对所有用户的登陆shell都有效；
用户主目录下的配置文件：为当前用户设置环境信息，仅对当前用户的登陆shell有效；

### 2.用户主目录下有哪些配置文件

用户主目录下的配置文件有.profile, .login, .bash_profile, .bash_login。不同的登陆SHELL用不同的配置文件，具体为：
Bourne Shell (sh)：使用.profile文件；
Korn Shell (ksh)：兼容sh，也使用.profile文件；
C Shell (csh)：使用.login文件；
Bourne Again Shell（bash）：bash被设计成兼容sh，ksh，并有csh的特征, 所以会在用户的主目录下依次查找.bash_profile，.bash_login，.profile文件，并用找到的第一个作为自己的配置文件；

bash查找这些同义文件的好处是，如果曾经用过Bourne shell，你可以保留它，如果需要加入特定的bash命令，可以将它们放入.bash_profile中并在后面跟一条命令source .profile。登录时，所有特定的bash命令均被执行，然后bash将会调用.profile，执行其保留的命令。即使决定仍使用Bourne shell，也不必修改已存在的文件，类似的方法也可以用于.bash_login和C shell的.login，但由于这些shell基本语法的差异性，这不是一个好主意。

需要注意的是：经过编辑的.bash_profile文件，直到退出再次登录，该文件被重新读取后，.bash_profile中的内容才会生效。

### 3.Bash的其他几个配置文件

Bash的设计考虑的场景比较齐全，所以除了上面的几个配置文件外，还有如下几个配置文件：

* ~/.bash_logout文件：每次登录shell退出时文件.bash_logout被读取并执行，它提供了定制用户环境的功能，如删除帐号内的临时文件或记录登录系统所花的时间；
* ~/.bashrc文件：.bash_profile只被登录shell读取并执行，如果你在命令行上键入bash启动一个新的shell，这个新shell读取的是.bashrc而不是.bash_profile，将登录shell和运行一个子shell所需的配置文件分开可以获得非常灵的配置策略，从而满足不同的应用场景；
* /etc/bashrc文件：和~/.bashrc的含义一样，只不过适用于所有的用户；

事实上，很多情况下某些配置文件是一样的，比如.bashrc和.bash_profile，这时只需要在一个配置文件中执行另外一个配置文件就可以了，例如source .bashrc，或 ..bashrc（匿名命令）。

## 二、Bash常见配置项


```sh
# 设置保存命令的数目
HISTSIZE=100000
HISTFILESIZE=100000

# 设置忽略连续重复的命令，如果需要设置清除所有重复的命令。要清除整个命令历史中的重复条目，可以将 HISTCONTROL 设置成 erasedups。
HISTCONTROL=ignoredups

# 设置历史记录的格式，保留时间戳
HISTTIMEFORMAT=%F %T

# 默认情况下，命令历史存储在 ~/.bash_history 文件中。下面这个设置可以指定自己的保存位置。
HISTFILE=/root/.commandline_warrior

```

使用Ctrl + R进行历史搜索。此快捷键让你对命令历史进行搜索，对于想要重复执行某个命令的时候非常有用。当找到命令后，通常再按回车键就可以执行该命令。如果想对找到的命令进行调整后再执行，则可以按一下左或右方向键。

快速重复执行上一条命令，有 4 种方法可以重复执行上一条命令：
* 1、使用上方向键，并回车执行。
* 2、按 !! 并回车执行。
* 3、输入 !-1 并回车执行。
* 4、按 Ctrl+P 并回车执行。
通过指定关键字来执行以前的命令，输入 !ps 并回车，将执行以 ps 打头的命令。

参考资料：
1、[Linux终端配置](http://elaner.com/forum.php?mod=viewthread&tid=111)
2、[Where is my bashrc](http://hintsforums.macworld.com/showthread.php?t=38560)
3、[配置文件的区别](http://www.vmman.net/2010/10/配置文件-profile-login-bash_profile和-bashrc的区别/)
4、[Shell命令的历史基础](http://notes.techlive.me/note-2009-05-04-12.html)