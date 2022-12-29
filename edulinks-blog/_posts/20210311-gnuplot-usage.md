---
title: GNUPLOT使用技巧总结
date: 2021-03-10 09:57:51
tags:
keywords: gnuplot, gnuplot linux, 作图, gnuplot 作图
description: Gnuplot 是一款非常著名的作图软件，支持Mac、Linux等多种操作系统类型，我曾写过「[Mac OS X下GnuPlot的安装](http://www.edulinks.cn/2011/07/03/20110703-install-gnuplot/)」以及「[利用Apache ab以及GNUPlot来进行Web测试](http://www.edulinks.cn/2011/07/25/20110725-ab-test/)」两篇文章来介绍如何使用这个工具。
---

Gnuplot 是一款非常著名的作图软件，支持Mac、Linux等多种操作系统类型，我曾写过「[Mac OS X下GnuPlot的安装](http://www.edulinks.cn/2011/07/03/20110703-install-gnuplot/)」以及「[利用Apache ab以及GNUPlot来进行Web测试](http://www.edulinks.cn/2011/07/25/20110725-ab-test/)」两篇文章来介绍如何使用这个工具。

最近翻出了之前整理的更为详细的使用说明，发布出来希望能帮大家熟悉这个工具。

一些最基本的操作请大家看说明书。这里总结一下我在使用过程中遇到的一些问题以及解决的办法，目的是让那些以前不会的或不熟练的能快速入门，会画自己想要的图，因为原来的说明书很长，较难有针对性地很快找到自己想要的信息。这里简单的总结不可能面面俱到，所以大家不要抱怨我写的不全，更全面的了解还是请看说 明书，网上的资料也多的是。其实这也是我们从网上一点一点搜集和摸索出来的。我相信看完后，应该平时最常见的问题基本上都能在这里找到答案。如果大家在使 用过程中摸索到了我没有写到的技巧和体会，或有其它建议，请大家提告诉我，以不断完善这篇总结，谢谢！

### **一、基础篇：**

在linux命令提示符下运行gnuplot命令启动，输入quit或q或exit退出。

**1、plot命令**

```sh
gnuplot> plot sin(x) with line linetype 3 linewidth 2 或
gnuplot> plot sin(x) w l lt 3 lw 2  
#用线画，线的类型（包括颜色与虚线的类型）是3，线的宽度是2，对函数sin(x)作图

gnuplot> plot sin(x) with point pointtype 3 pointsize 2 或
gnuplot> plot sin(x) w p pt 3 ps 2  
#用点画，点的类型（包括颜色与点的类型）是3，点的大小是2

gnuplot> plot sin(x) title 'f(x)' w lp lt 3 lw 2 pt 3 ps 2  
#同时用点和线画，这里title ‘f(x)’表示图例上标'f(x)'，如果不用则用默认选项

gnuplot> plot sin(x)  
#此时所有选项均用默认值。如果缺某一项则将用默认值

gnuplot> plot ‘a.dat’ u 2:3 w l lt 3 lw 2 
#利用数据文件a.dat中的第二和第三列作图
```

顺便提一下，如这里最前面的两个例子所示，在gnuplot中，如果某两个词，按字母先后顺序，前面某几个字母相同，后面的不同，那么只要写到第一个不同的字母就可以了。如with，由于没有其它以w开头的词，因此可以用 w 代替，line也可以用 l 代替。

### **2、同时画多条曲线**

```sh
gnuplot> plot sin(x) title ‘sin(x)’ w l lt 1 lw 2, cos(x) title ‘cos(x)’ w l lt 2 lw 2 
#两条曲线是用逗号隔开的。画多条曲线时，各曲线间均用逗号隔开就可以了。
```
以上例子中是对函数作图，如果对数据文件作图，将函数名称换为数据文件名即可，但要用单引号引起来。

### **3、关于图例的位置**

默认位置在右上方。
```sh
gnuplot> set key left 
# 放在左边，有left 和right两个选项
gnuplot> set key bottom 
# 放在下边，只有这一个选项；默认在上边
gnuplot> set key outside 
# 放在外边，但只能在右面的外边
```

以上三个选项可以进行组合。如：
```sh
gnuplot> set key left bottom #表示左下边
```

还可以直接用坐标精确表示图例的位置，如
```sh
gnuplot> set key 0.5,0.6 #将图例放在0.5,0.6的位置处
```

### **4、关于坐标轴**

```sh
gnuplot> set xlabel ‘x’  #x轴标为‘x’
gnuplot> set ylabel ‘y’  #y轴标为’y’
gnuplot> set ylabel ‘DOS’ tc lt 3 #其中的tc lt 3表示’DOS’的颜色用第三种颜色。
gnuplot> set xtics 1.0  #x轴的主刻度的宽度为1.0，同样可以为y轴定义ytics
gnuplot> set mxtics 3  #x轴上每个主刻度中画3个分刻度，同样可以为y轴定义mytics
gnuplot> set border 3 lt 3 lw 2 #设为第三种边界，颜色类型为3，线宽为2
```

同样可以为上边的x轴（称为x2）和右边y（称为y2）轴进行设置，即x2tics，mx2tics，y2tics，my2tics。
```sh
gnuplot> set xtics nomirror
gnuplot> unset x2tics   #以上两条命令去掉上边x2轴的刻度
gnuplot> set ytics nomirror
gnuplot> unset y2tics   #以上两条命令去掉右边y轴的刻度
```

### **5、在图中插入文字**

```sh
gnuplot> set label ‘sin(x)’ at 0.5,0.5 #在坐标（0.5,0.5）处加入字符串’sin(x)’。
```

在输出为.ps或.eps文件时，如果在set term 的语句中加入了enhanced选现，则可以插入上下标、希腊字母和特殊符号。上下标的插入和latex中的方法是一样的。

### **6、在图中添加直线和箭头**

```sh
gnuplot> set arrow from 0.0,0.0 to 0.6,0.8  #从（0.0,0.0）到（0.6,0.8）画一个箭头
gnuplot> set arrow from 0.0,0.0 to 0.6,0.8 lt 3 lw 2  #这个箭头颜色类型为3，线宽类型为2
gnuplot> set arrow from 0.0,0.0 to 0.6,0.8 nohead lt 3 lw 2 #利用nohead可以去掉箭头的头部，这就是添加直线的方法。
```

注意，在gnuplot中，对于插入多个的label和arrow等等，系统会默认按先后顺序分别对各个label或arrow进行编号，从1开始。如果以后要去掉某个label或arrow，那么只要用unset命令将相应的去掉即可。如：
```sh
gnuplot> unset arrow 2
```
将去掉第二个箭头。

### **7、图的大小和位置**
```sh
gnuplot>set size 0.5,0.5 
# 长宽均为默认宽度的一半，建议用这个取值，尤其是画成ps或eps图形的时候
gnuplot>set origin 0.0,0.5  
# 设定图的最左下角的那一点在图形面板中的位置。这里图将出现在左上角。
```

### **8、画三维图**
```sh
gnuplot>splot ‘文件名’ u 2:4:5 
# 以第二和第四列作为x和y坐标，第五列为z坐标。
```

### **9．将图形输出到文件**

在 gnuplot中，输出文件的格式是由terminal来控制的。默认的情况下，都是输出到屏幕，即终端模式为X11。如果想输出到文件，则必须对 terminal进行设置。要了解有那些终端类型，可以输入 set terminal后回车，所有支持的终端模式（或文件格式）都列出来了。就我来说，输出文件的格式用得最多的是ps和eps文件。这在运行画图命令 plot或splot前必须先运行如下两条命令：
```sh
gnuplot>set term post eps color solid enh
gnuplot>set output ‘a.eps’
```

其 中第一条命令为设置终端模式，post即为postscript模式，这是输出到ps或eps文件必须有的。后面的eps，color，solid以及 enh选项均可以有或没有，根据你的需要。eps表示输出为eps文件，没有就输出为ps文件；color表示输出图形为彩色，不用就会保存为黑白 的；solid表示输出图中的所有线都用实线，不用则除了第一条线为实线外，其它的均用不同的虚线；使用enh(enhanced)选项可以在图中插入上 下标、希腊字母和特殊符号，不用则不能实现这些功能。后面的选项可以根据自己的需要选择一个或几个。

第二条命令对output的设置表示要输出的文件的名字。但是请注意，运行完这条命令后，还仅仅是定义了输出文件的名字，实际上，图还没画到这个文件里去。因此运行这两条命令还只是进行了必须的设置。然后运行如下命令
```sh
gnuplot>plot sin(x) w l
```
或
```
gnuplot>replot（假如前面已经运行过plot或replot命令的话）
```
这时，图形就在上面指定的文件中了。

要输出为其它格式，同样要进行这样的设置，比如要输出为jpg格式，则在运行画图命令前先运行如下命令：
```sh
gnuplot>set term jpeg
gnuplot>set output ‘a.jpg’
```

如果要由其它模式再返回到输出到屏幕，则运行如下命令：
```sh
gnuplot>set term X11
```

### **二、提高篇：**

### **1、如何在同一张图里同时画多个图**
```sh
gnuplot>set multiplot  #设置为多图模式
gnuplot>set origin 0.0,0.5  #设置第一个图的原点的位置
gnuplot>set size 0.5,0.5 #设置第一个图的大小
gnuplot>plot “a1.dat”
gnuplot>set origin 0.5,0.5  #设置第二个图的原点的位置
gnuplot>set size 0.5,0.5  #设置第二个图的大小
gnuplot>plot “a2.dat”
gnuplot>set origin 0.0,0.0  #设置第三个图的原点的位置
gnuplot>set size 0.5,0.5 #设置第三个图的大小
gnuplot>plot “a3.dat”
gnuplot>set origin 0.5,0.0  #设置第四个图的原点的位置
gnuplot>set size 0.5,0.5 #设置第四个图的大小
gnuplot>plot “a4.dat”
```

当然，如果后一个图中的某个量的设置和前一个的相同，那么后一个中的这个量的设置可以省略。例如上面对第二、第三和第四个图的大小的设置。前一个图中对某个量的设置也会在后一个图中起作用。如果要取消在后面图中的作用，必须用如下命令，如取消label，用
```sh
gnuplot>unset label
```

### **2、作二维图时，如何使两边坐标轴的单位长度等长**
```sh
gnuplot> set size square  #使图形是方的
gnuplot> set size 0.5,0.5  #使图形是你要的大小
gnuplot> set xrange[-a:a]
gnuplot> set yrange[-a:a]  #两坐标轴刻度范围一样
gnuplot> plot ‘a.dat’
```

### **3、如何在同一张图里利用左右两边的y轴分别画图**
```sh
gnuplot> set xtics nomirror  #去掉上面坐标轴x2的刻度
gnuplot> set ytics nomirror  #去掉右边坐标轴y2的刻度
gnuplot> set x2tics    #让上面坐标轴x2刻度自动产生
gnuplot> set y2tics   #让右边坐标轴y2的刻度自动产生
gnuplot> plot sin(x),cos(x) axes x1y2  #cos(x)用x1y2坐标，axes x1y2表示用x1y2坐标轴
gnuplot> plot sin(x),cos(x) axes x2y2   #cos(x)用x2y2坐标，axes x2y2表示用x2y2坐标轴
gnuplot> set x2range[-20:20]  #设定x2坐标的范围
gnuplot> replot
gnuplot> set xrange[-5:5] #设定x坐标的范围
gnuplot> replot
gnuplot> set xlabel 'x'
gnuplot> set x2label 't'
gnuplot> set ylabel 'y'
gnuplot> set y2label 's'
gnuplot> replot
gnuplot> set title 'The figure'
gnuplot> replot
gnuplot> set x2label 't' textcolor lt 3  %textcolor lt 3 #或 tc lt 3设置坐标轴名称的颜色
```

### **4、如何插入希腊字母和特殊符号**

一般只能在ps和eps图中，且必须指定enhanced选项。在X11终端（即显示器）中无法显示。
```sh
gnuplot> set terminal postscript enhanced然后希腊字母就可以通过{/Symbol a}输入。例如
gnuplot> set label ‘{/Symbol a}’
<!--[if !supportLineBreakNewLine]-->
<!--[endif]-->
```

各种希腊字母与特殊符号的输入方法请见安装包中gnuplot-4.0.0/docs/psdoc目录下的ps_guide.ps文件。
另外还可参见：http://t16web.lanl.gov/Kawano/gnuplot/label-e.html#4.3

### **5、gnuplot中如何插入Angstrom（埃）这个符号(A上面一个小圆圈)**

脚本中在插入前先加入
```sh
gnuplot>set encoding iso_8859_1
```
这个命令，然后就可以通过“{\305}”加入了。如横坐标要标上“k(1/?)”：
```sh
gnuplot>set xlabel 'k(1/{\305})
```
如果是multiplot模式，则这个命令必须放在
```sh
gnuplot>set multiplot
```
的前面。

如果后面还要插入别的转义字符，那么还要在插入字符后加入如下命令：`set encoding default`安装包中gnuplot-4.0.0/docs/psdoc/ps_guide.ps文件中的表中的‘E’代表那一列的所有符号都用这个方法输入。

### **6、gnuplot画等高线图**
```sh
gnuplot>splot ‘文件名.dat’ u 1:2:3 w l  %做三维图
gnuplot>set dgrid3d 100,100 %设置三维图表面的网格的数目
gnuplot>replot
gnuplot>set contour  %设置画等高线
gnuplot>set cntrparam levels incremental -0.2,0.01,0.2  %设置等高线的疏密和范围，数据从  -0.2到0.2中间每隔0.01画一条线
gnuplot>unset surface  去掉上面的三维图形
```

最后用鼠标拽动图形，选择合理的角度即可。或者直接设置(0,0)的视角也可以：
```sh
gnuplot>set view 0,0
gnuplot>replot
```

这里注意，画三维图的数据文件必须是分块的，也就是x每变换一个值，y在其变化范围内变化一周，这样作为一块，然后再取一个x值，y再变化一周，作为下一数据块，等等。块与块之间用一空行格开。

### **7、如何画漂亮的pm3d图**
```sh
gnuplot> set pm3d        # 设置pm3d模式
gnuplot> set isosamples 50,50    # 设置网格点
gnuplot> splot x**2+y**2     # 画三维图
gnuplot> splot x**2+y**2 w pm3d  # 画成pm3d模式，注意比较变化
gnuplot> set view 0,0       # 设置视角，（0，0）将投影到底面上去
gnuplot> splot x**2+y**2 w pm3d  # 重画，注意看变化
gnuplot> unset ztics        # 把z轴上的数字给去掉
gnuplot> set isosamples 200,200   # 使网格变细
gnuplot> replot          # 重画，注意看变化，主要是过渡更光滑
```

### **8、利用脚本文件避免重复输入**

有时候对某个数据文件做好一张图后，下次可能还要利用这个数据文件作图，但某个或某些设置要作些细微变化。这时候，可以把第一次作图时的命令全部写到一个文件里，如a.plt，下次只要将相应的设置做修改后，用下面的命令就会自动运行文件所有的命令而最后得到你要的图：
```sh
gnuplot>load ‘a.plt’
```
作为一个例子，假设文件名为a.plt，里面的内容为：
```sh
set pm3d 
set view 0,0 
unset ztics 
set isosamples 200,200 
splot x**2+y**2 w pm3d
set term post color
set output ‘a.ps’
replot
```

那么启动gnuplot后，只要运行如下命令就可以了：
```sh
gnuplot>load ‘a.plt’
```

如果我们要得到的仅仅是.ps或.eps图，那也可以在linux命令提示符下直接运行如下命令：

[zxh@theory zxh]$gnuplot a.plt

### **9、在gnuplot模式下运行linux命令**

在gnuplot提示符下也可以运行linux命令，但必须在相应的命令前面加上 ! 号。例如，假设很多参量都已经设置好了，但需要对某个数据文件a.dat进行修改后再画图，则可以用如下方式

gnuplot>!vi a.dat

通过这种方式，所有的linux命令都可以在gnuplot环境里运行。

另外，也可以在gnuplot的提示符后输入shell，暂时性退出gnuplot，进入linux环境，做完要做的事情后，运行exit命令，又回到gnuplot环境下。

```sh
gnuplot>shell
[zxh@theory zxh]$vi a.f
[zxh@theory zxh]$f77 a.f
[zxh@theory zxh]$a.out  (假设生成a.dat数据文件)
[zxh@theory zxh]$exit
gnuplot>plot ‘a.dat’ w l
```
