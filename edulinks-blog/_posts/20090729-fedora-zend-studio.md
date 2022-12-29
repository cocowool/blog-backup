---
title: Fedora下Zend Studio 6.1.2 的配置
date: 2009-07-29 22:05:01
tag: 
keywords: zend studio, zend, fedora, linux
description: Zend Studio是我们PHPer的一个非常的得力的工具，有了他往往能够让我们的工作事倍功半，今天就来记录一下日常使用中非常有用的配置，来帮助更多的PHPer提高工作的效率。
---

Zend Studio是我们PHPer的一个非常的得力的工具，有了他往往能够让我们的工作事倍功半，今天就来记录一下日常使用中非常有用的配置，来帮助更多的PHPer提高工作的效率。

1、配置服务器。

如果我们希望能够快速的进行调试，那么将Server和Zend整合在一起是必不可少的。配置服务器的入口很多，比如我们可以从Preferences/PHP/PHP Server中进入，也可以在Run按钮的下拉列表中找到Run Configuration/Server的配置。
创建新的Server时，我们需要指定Server访问的地址，这个如果我们在本地配置了服务器的话，我们可以到(/etc/httpd/conf.d/vhost)中设置本地的工程的域名，当然，不能忘记设置HOSTS（在/etc/hosts中）。

配置完成后，如果需要调试页面，我们只需要点击“Run”图标，第一次的时候会弹出一个地址对话框等待我们确认，之后点击就可以在Zend Studio中直接查看代码的运行结果了，这个对于逻辑比较重要的页面来说非常的实用，因为我们不用像往常那样切换到浏览器中，点击F5刷新查看结果了。

2、创建PHP工程。

我发现在Fedora下，如果你创建了一个一般的工程，PHP的函数是不会有自动的代码提示的，后来找了很久并没有发现在什么地方可以设置。但是如果将工程创建为PHP Project，那么我们就可以享受到代码提示带来的快感了。
创建PHP Project更重要的一个好处还在于，当我们的工程越来越庞大的时候，我们就可以很容易的在各个类和各个函数之间进行跳转，只需要两个按键“Ctrl+Left Click”。

3、DEBUG。

由于是解释执行型语言，PHP的调试一直都不是很方便，但是我们可以通过 Zend Studio 的Debug来改善这一状况。Debug 的设置窗口可以在 Window/Preferences/PHP/Debug中找到。
默认的一些配置：

PHP Debugger：    默认的使用Zend Debugger，需要我们安装Zend Debugger后才能使用。Zend Studio 6.1.2 默认进行了安装，可以通过Window/Preference/PHP/Debug/Installed Debuggers进行查看。
Zend Debugger配置过程中需要有一个dummy.php文件，这个文件的作用是在指定的服务器上建立一个调试脚本的会话（SESSION），文件名最好默认不要修改。
PHP Server:    选择我们之前创建好的服务器。
PHP Executable：选择合适的PHP版本，如果需要添加新的项目，可以在Window / Preference / PHP / PHP Executable中找到。
最后还有两项输入输出字符编码配置，这个采用默认就好了，下面就可以进行调试了。

Zend Studio的调试允许我们设置断点、按步执行、检测变量和参数，下面分别说明一下几种不同的调试方法。

3.1 PHP代码本地调试（PHP Script Local Debugging）。
允许我们通过Zend Studio 内置的Debugger进行调试，一般在部署到服务器之前我们可以先用这项来检查程序中是否存在问题。本地调试需要代码存在于工作目录中，同时本地调试回默认使用Zend Studio自带的PHP Executable环境。
调试过程一般为：
3.1.1    在我们的代码中设置断点。
3.1.2    保存文件。
3.1.3    点击Debug旁的箭头进行Debug配置，或者邮件选择 Debug As ... / Debug Configurations 进行配置。
3.1.4    如果没有设置断点，也可以通过在配置中选择Break at first line来进行调试。
3.1.5    如果需要，我们还可以在调试代码中加入变量，之后运行就会跳转到调试视图。在调试试图，我们通过单击“Resume"、“Step Into“等可以控制程序的执行，同时还可以在变量窗口中观察到变量的变化。

3.2 PHP代码远程调试（PHP Script Remote Debugging）。
这个选项允许我们使用服务器上安装的Zend Debugger来调试工作目录下的文件。使用这个功能，我们必须在服务器上安装Zend Debugger。
调试的过程和3.1基本类似，现在针对服务器上的Zend Debugger安装做一些说明。

配置Remote Debugging首先需要确保你的服务器上安装了Zend Debugger，你可以从下面这个地址中下载到：http://www.zend.com/en/products/studio/downloads。
根据自己系统的环境选择合适的发行包，我这里选择的是    ZendDebugger-5.2.14-linux-glibc23-i386 ，解压后是几个目录和一个dummy.php文件和其他的一些说明。根据自己当前的PHP版本选择合适的文件夹（不知道的可以用php -v来进行查看，这需要你已经将PHP命令放入环境变量中），找到 ZendDebugger.so 的路径，然后添加到 php.ini 中，格式 zend_extension=/usr/lib/php/modules/ZendDebugger.so。
同时添加下面两行代码：
zend_debugger.allow_hosts=<ip_addresses>
zend_debugger.expose_remotely=always
完成后将dummy.php拷贝到工作目录中，然后重启服务器就可以了。这个时候，如果我们再用 phpinfo() 查看服务器情况的时候，就能够看到 Zend Debugger 这一项。

3.3 调试PHP网页。
这个选项允许我们调试位于服务器上的代码，这样我们就可以全面的测试代码的运行状况。

3.4 URL Debugging。
用来调试远程服务器上的代码，通过输入一个网址来进行调试，所以并不要求文件在本地存在。

3.5 工具栏调试（Toolbar Debugging）。
这一项应该属于比较新的产品，目前安装Zend Studio 之后，会提示是否安装Firefox Toolbar 这样，在浏览器中也可以方便的调用 Zend 提供的一些调试方法。

4、代码提示（代码提示）。
代码提示帮助我们找到变量、调用函数、找到对象成员、引用文件等，能够极大的提高开发的速度，在Win下使用时，代码提示默认是打开的。在Feodra下使用，根据我的经验，需要在PHP的工程中，代码提示才能起作用。在Zend Studio 7.0中，需要Ctrl + Space才能调出代码提示。

5、性能分析（Profile）。
通过性能分析，我们能够找到代码中的瓶颈所在，从而提高代码的执行效率。Profile为我们提供了相近的统计资料和分析，我想如果用过Firebug的Profile功能的用户，对于使用 Zend Studio 的这个功能，应该也不会陌生。

当然，Zend Stduio 使用中还会有各种各样的可以提高效率的办法，诸如快捷键的设置，在此我也无法一一总结，只要我们平时在使用中能够有足够的心思，多用勤用 Zend Help，如果是在不会也要学会通过 Google 解决问题，我们开发的效率一定会快速提高，从而成为 Senior PHPer 指日可待。

参考资料
1、Zend Help











