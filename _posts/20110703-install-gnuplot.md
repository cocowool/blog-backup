---
title: Mac OS X下GnuPlot的安装
date: 2011-07-03 21:22:01
tag: 
keywords: gnuplot, mac
description: gnuplot作为一个非常著名的作图软件，可以和apache的ab测试结果结合来进行性能测试的绘图，对于WEB从业者来说，如果能够数量的掌握，必定能够提高大家呈现数据和说服别人的能力。
---

gnuplot作为一个非常著名的作图软件，可以和apache的ab测试结果结合来进行性能测试的绘图，对于WEB从业者来说，如果能够数量的掌握，必定能够提高大家呈现数据和说服别人的能力。

Gnuplot是一个科学界广泛使用的作图软件，从Unix软件发展而来，是一款免费软件。因为其强大的作图功能，逐渐也有其他行业的人来维护支持这个软件，使其变的越来越流行。


Mac下的安装：

Mac下的Gunplot有两个主要的形式：第一个就是Carbon二进制的文件，如果你运行的是经典的Mac OS系统（OS X之前），那这个可能是你唯一的选择。这个版本也可以在OS X下运行。如果你只是希望能够看到一个gnuplot文件的输出结果，而不需要安装软件到系统上，那么使用这个版本会非常方便。它不需要X-Windows或者其他的软件，安装简便，但是缺乏一些外部的控制。他还有另外一些特性：

gnuplot for Mac支持多种苹果特有的技术。例如，gnuplot可以使用AppleScript或者被记录，支持PICT和QuickTime的电影格式，支持命令行编辑器，而且支持拖拽。仅能运行在Mac OS 8.6 系统之上，必须有Color QuickDraw、QuickTime、CarbonLib1.1或更高。程序在Mac OS 9.0，Mac OS 9.1 和 Mac OS X下测试通过。
Carbon Gnuplot的下载地址为：[download](http://web.me.com/chibaf/math/carboncocoa/)这个地址中也包含了很多非常有用的工具。

传统方式自然就是源代码编译安装。如果想在Mac OS下严肃的使用GNUPlot，那么还是推荐使用传统的编译安装的方法。因为Mac OS是一个Unix的系统，所有已跨平台为目的编写的代码都可以编译安装。当然，这里面包括Gunplot。Gunplot的源码[official Gnuplot v. 4.0 sources](http://www.gnuplot.info/)。


以上的这些信息获取自参考资料中的第二篇文章，了解到这些资料之后，我仍然觉得，从源码开始安装自己的gnuplot将会是一个更好的过程，也许会艰苦一些，但是我非常愿意尝试。下面就记录了具体的安装过程。


后记：整个安装的过程超出我的想象，因为众多的依赖包的缺失。而且Mac OS X在缺少包管理的情况下，不会像Fedora或者Ubuntu那样，通过一个 yum或者apt-get将这个问题解决。我尽量避免了在自己的系统中安装包管理系统，如果你不喜欢从源码安装，可以考虑 DarwinPorts 或者 Fink。这里，列出安装过程中需要的依赖包以及版本。

> 系统：Mac OS X 10.6.8，安装了xCode；
>
> * gnuplot 4.4.3
> * zlib 1.2.5
> * libjpegv8c
> * FreeType2.4.5
> * gd2.0.35
> * libpdf（如果需要支持输出结果为pdf）

具体的安装过程：
1、首先系统中需要安装 xCode 和X11的库文件。这个的主要目的是安装gcc，如果你不喜欢安装xCode，可以使用其他的方式只安装GNU开发工具。

2、zlib
解压缩后进入文件夹，执行
`./configure --shared && make && sudo make install`

3、libpng
libpng也是libgd依赖的包，但是Mac系统中已经自带了这个，所以不再需要单独安装

4、libjpegv8c，下载解压缩后进入目录执行
```sh
export MACOSX_DEPLOYMENT_TARGET=10.6
./configure --enable-shared && make && sudo make install
```

5、FreeType2.4.5，下载解压缩后进入目录执行
```sh
./configure && make && sudo make install
```

6、gd2.0.35，下载解压缩后进入目录，分步执行
```sh
sudo ln -s /usr/X11R6/include/fontconfig /usr/local/include
ln -s `which glibtool` ./libtool
./configure --with-png=/usr/X11 --x-includes=/usr/X11/include --x-libraries=/usr/X11/lib
```

configure阶段的这些参数和步骤，是为了确保gd能够找到对应的库文件，确认所有的支持都找到后，就可以接着下面的安装了。
```sh
** Configuration summary for gd 2.0.34:
Support for PNG library:          yes
Support for JPEG library:         yes
Support for Freetype 2.x library: yes
Support for Fontconfig library:   yes
Support for Xpm library:          yes
Support for pthreads:             yes

make && sudo make install
./gdtest test/gdtest.png
```

7、gnuplot，下载解压缩后进入目录执行
`./configure`

执行后检查是否已经支持了图片类的输出。
```sh
jpeg terminal: yes
gif terminal: yes (with animated gif)
png terminal: yes
(jpeg, gif and png terminals can use TTF fonts)

make && sudo make install
```

8、安装Aqua term

完成之后，就可以通过 `gnuplot,plot sin(x)` 查看作图的效果了

![](/20110703-install-gnuplot/xxx.png)

参考资料：
1、[gnuplot homepage](http://www.gnuplot.info/)
2、[Mac OS X安装Gunplot](http://mac-tone.blogspot.com/2009/04/gnuplot-on-mac-osx.html)
3、[Installing gnuplot on Mac OS X 10.6](http://www.dansanderson.com/blog/2011/03/installing-gnuplot-on-mac-os-x-106.html)
4、[http://www.libgd.org/DOC_Compiling_GD_on_Mac_OS_X_HOWTO](http://www.libgd.org/DOC_Compiling_GD_on_Mac_OS_X_HOWTO)
