---
title: 神奇的tmux
date: 2021-05-13 13:28:15
tags:
keywords: tmux, terminal, linux
description: 
---

本文适合于对于脱离鼠标有着强烈愿望的开发、运维以及一切工作在*nix环境下的用户。它提供了一个在单一环境下打开多个终端、窗口的方式。对于大多数现代的终端以及终端工具来说，基本上都提供了Tab的功能，但是tmux仍有着无可比拟的魅力，让你最终喜欢上她。

1、安装

我采用了源代码的安装方式，系统版本Mac OS X Mountain Lion。安装需要lib event库支持，如果提示没有，需要先安装libevent。安装过程比较简单，无非是./configure、make、make install。所有配置采用默认的就好了，完成后如果能够看到 tmux -V返回信息，说明就安装成功了。

快捷键约定

我们的系统默认提供了很多快捷键，形式大多为PREFIX＋Shortcut Key，例如我们非常熟悉的Ctrl ＋ C、Ctrl ＋ V。为了避免同系统的其他快捷键冲突，tmux的PREFIX默认是Ctrl＋b。在使用tmux的快捷键时，我们需要首先一起按下Ctrl＋B，然后再按其他的Shortcut Key。

2、基本用法

会话:

一个会话相当于我们用SecureCRT或者Terminal开一个窗口。
tmux钟创建会话的方法是：tmux new-session，这样将会创建一个默认命名的会话。如下图所示

![Screen Shot 2012-08-21 at 下午9.17.30](20210513-tmux-notes/Screen%20Shot%202012-08-21%20at%20%E4%B8%8B%E5%8D%889.17.30.png)

也可以使用tmux new的方式来减少输入的内容。还可以使用tmux new -s basic来讲会话命名为basic。

会话的挂起和恢复。我们可以通过Ctrl＋B > d 的方式来将一个会话放入后台，这样如果我们的终端窗口不小心被关闭了，我们放在会话中的程序并不会退出（Linux中有screen命令也可以实现同样的效果）。我们新打开一个窗口后，可以通过tmux attach -t session-name来恢复刚才放入后台的会话。很神奇吧，有点像Mac Lion的开机后打开原来的窗口 :)。

窗口:

窗口的概念直观上理解相当于我们在SecureCRT中新建的Tab，但是又有区别。我们在SecureCRT或者终端新建多个Tab时，实际上打开了多个SSH连接，如下图所示，可以利用w看到有四个终端连接到了服务器上。

![Screen Shot 2012-08-21 at 下午9.22.31](20210513-tmux-notes/Screen%20Shot%202012-08-21%20at%20%E4%B8%8B%E5%8D%889.22.31.png)

然而在tmux新建多个窗口后，还是只有一个SSH连接。tmux新建窗口的快捷键是 Ctrl+b > c 如下图所示：

![Screen Shot 2012-08-21 at 下午9.19.57](20210513-tmux-notes/Screen%20Shot%202012-08-21%20at%20%E4%B8%8B%E5%8D%889.19.57.png)



![Screen Shot 2012-08-21 at 下午9.22.31-0903681](20210513-tmux-notes/Screen%20Shot%202012-08-21%20at%20%E4%B8%8B%E5%8D%889.22.31-0903681.png)


同会话一样，窗口也可以有名字。可以在创建时通过 tmux new -s basic -n first 指定，可以通过创建后通过 Ctrl + b > ,来重新命名。窗口切换的快捷键：Ctrl + n 打开下一个窗口，Ctrl + p 打开前一个窗口，也可以通过 Ctrl + b > [0-9] 打开指定的窗口。如果窗口太多，还可以通过 Ctrl + b > f来搜索窗口。Ctrl + b > w 来显示一个窗口列表供用户选择。

Panes:

我觉得这是tmux提供的最有用的功能之一，可以将当前窗口进行分割，如下图所示：

![Screen Shot 2012-08-21 at 下午9.26.08](20210513-tmux-notes/Screen%20Shot%202012-08-21%20at%20%E4%B8%8B%E5%8D%889.26.08.png)


tmux的退出

退出的方式很简单，输入exit我们就可以退出当前的编辑窗口。退出时按照page>窗口>会话的顺序依次退出。

3、tmux的配置。

tmux的默认配置对于一般用户来说不太友好，我们可以通过配置文件来进行tmux的自定义。配置文件可以分为全局配置文件（/etc/tmux.conf）和用户配置文件（~/.tmux.conf）。默认首先加载全局配置文件，如果没有则加载用户配置文件。

参考资料：
1、tmux
2、libevent