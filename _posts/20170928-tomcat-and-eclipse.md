---
title: Mac环境下配置Tomcat+Eclipse
date: 2017-09-28 11:02
tag: 
categories: 开发
keywords: mac, tomcat, eclipse, mac eclipse
description: mac下配置Tomcat和Eclipse的开发环境。
---




### 下载Tomcat
首先在[Tomcat官方网站](http://tomcat.apache.org/download-70.cgi)找到自己合适的版本，下载 tar.gz 版本的，下载完成后解压缩到一个目录，进入这个目录下的 bin 执行 startup.sh，如果看到下面的界面，表示已经启动了。
![](/20170928-tomcat-and-eclipse/39469-20170928110034653-2083478430.png)
打开浏览器，输入[http://localhost:8080](http://localhost:8080)能够看到界面显示，表示安装已经成功了。
![](/20170928-tomcat-and-eclipse/39469-20170928110047778-325300410.png)

> 我使用的是7.0.81的版本，最新的版本是9。


### 配置Eclipse
启动Eclipse后，选择 Preferences(快捷键```⌘+,```)
![](/20170928-tomcat-and-eclipse/39469-20170928110100903-1853525836.png)
选择 Server->Runtime Environments，点击 Add 添加
![](/20170928-tomcat-and-eclipse/39469-20170928110118262-1553876600.png)
选择合适的Tomcat版本，点击下一步，然后找到自己刚才下载的Tomcat位置，点击完成。
![](/20170928-tomcat-and-eclipse/39469-20170928110130778-780724613.png)
今后新建了项目之后，就可以运行在这个Server上进行预览测试了。

> 我使用的是 Eclipse Oxygen

**本文为作者原创，如果您觉得本文对您有帮助，请随意打赏，您的支持将鼓励我继续创作。**
![](/20170928-tomcat-and-eclipse/39469-20170825142904777-1467390737.png)
参考资料：
1、[Apache Tomcat](http://tomcat.apache.org/download-70.cgi)
2、[Eclipse](http://www.eclipse.org/home/index.php)












