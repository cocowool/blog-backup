---
title: Sublime Text 3 提高工作效率的使用技巧
date: 2016-12-02 09:43:01
tag: 
keywords: sublime, sublime text, sublime text技巧
description: Sublime Text 3对于Sublime Text 2压倒性的优势就是秒启动，再掌握点技巧就更无敌了。
---

Sublime Text 3对于Sublime Text 2压倒性的优势就是秒启动，启动非常非常快，所以从2012年到2016年我一直用[Sublime Text 2](http://www.cnblogs.com/cocowool/archive/2012/07/01/2572011.html)，但是安装了3并且启动试用后，我再也不愿意打开Sublime Text 2了。

工欲善其事，必先利其器。经过多年使用，总结了一些Sublime Text的使用技巧。

一、常用的使用技巧。

0、Ctrl + P 匹配项目中的文件，然后快速跳转到文件中。找到对应文件后，还可以在完整的文件名后通过输入跳转到更精确的位置：
@符号跳转：输入@symbol，可以跳转到symbol符号所在位置。支持实时预览。在PHP文件中，@将显示所有的函数列表。
#关键字跳转：输入#keyword跳转到keyword所在的位置。
：行号跳转，输入：12跳转到文件的第12行。

1、快速打开、关闭左侧的文件列表。默认快捷键 Ctrl+k+b （Windows）Command + k + b （Mac）。也可以使用 View->Side Bar切换。

2、通过 Control + R 查看当前文件中的函数列表，Ctrl + G 定位到文件的指定行。

3、双击选中光标所在的单词，三击选中光标所在行，快捷键 Ctrl + L （Windows）Command + L （Mac）。

4、Ctrl + Shift + T 打开之前关闭的 tab 页，这对于大项目中不小心关闭了文件时非常有用，当然也可以通过 File -> Open Recent 找回。

5、批量行的编辑。如果需要对多个行同时进行修改，可以选中多行之后通过 Ctrl + Shift + L 将选中区域打散，然后再进行同时编辑。

6、分屏。如果需要分割窗口对比两个文件，可以使用分屏功能。

7、快速调整字体大小。开发的时候字体大小非常重要，如果觉得不舒服，可以使用 Ctrl + = 号来快速调大，或 Ctrl + Shift + = 调小，非常方便。

8、括弧跳转。对于一个大的函数，如果需要从开头跳到结尾，可以使用 Ctrl + M。

9、Ctrl + P，这个不需要多说了，菜单上就叫做 Goto Anywhere，可以很方便的实现日常工作中文件的查找跳转，当前前提是你要把整个工程文件夹打开。

二、挑选顺手的插件

Sublime Text 有强大的可扩展性，可以根据自己的需要安装不同的插件，这样她就会变得更加强大了。Sublime Text 3具有两种插件安装方式。

1、直接安装

可以直接搜索到自己需要的插件，然后下载安装包解压缩到 Packages 目录。

2、使用Package Control组件安装。

通过 Ctrl + · 调出命令行，输入：

import urllib.request,os; pf = 'Package Control.sublime-package'; ipp = sublime.installed_packages_path(); urllib.request.install_opener( urllib.request.build_opener( urllib.request.ProxyHandler()) ); open(os.path.join(ipp, pf), 'wb').write(urllib.request.urlopen( 'http://sublime.wbond.net/' + pf.replace(' ','%20')).read())


重启Sublime Text 3。如果在Perferences->package settings中看到package control这一项，则安装成功。

完成之后我安装了自己最常用的一个插件：

Emmet（Zend Coding）它的使用方式可以参考我之前的一篇博客[Sublime Text 2 以及 Zen Coding](http://www.cnblogs.com/cocowool/archive/2012/07/01/2572011.html)。

参考资料：1、[如何优雅的使用SublimeText](http://www.jeffjade.com/2015/12/15/2015-04-17-toss-sublime-text/#)
2、[Sublime Text Package Control](https://packagecontrol.io/installation)
3、[Sublime Text 3 插件：增强篇](http://www.jianshu.com/p/5905f927d01b)
3、[20个强大的Sublime Text 插件](http://www.open-open.com/news/view/26d731)
4、[Sublime Text 下的Markdown写作](http://www.cnblogs.com/jadeboy/p/4165449.html)
5、[追求高效工作的一路折腾](http://www.jeffjade.com/2015/08/28/2015-08-28-Write-Morkdown/)