---
title: Ubuntu下使用SVN
date: 2008-11-10 20:15:14
updated: 2020-06-10 08:09:00
tag: 
---

> 本文写于2008年，其中Eclipse相关的部分可能已经过时了，但是svn的命令仍旧有效。随着Git流行，SVN使用的人越来越少，但仍有其适用的场景和空间。

SVN作为日常开发中不可缺少的工具，今天终于开始在Ubuntu下使用了。
1、首先需要安装SVN。Ubuntu下的SVN安装十分简单，sudo apt-get install subversion，然后根据提示一步一步，就完成了SVN的安装；

2、检出文件（checkout）。
使用命令：svn co http://{svn repository url} /destination

然后系统会用当前的用户名登录，提示输入密码，如果第一次密码输入错误，会提示你输入用户名；
输入正确后，就可以检出文件了。

3、提交文件（commit）。
进入需要更新的目录，输入命令：
svn commit -m path-to-commit，其中path-to-commit可以为空，成功后会提示更新后的版本号。
4、更新文件（update）。

svn update，在要更新的目录运行这个命令就可以了。
5、查看日志（log）。

svn log path

6、为Eclipse配置SVN。
http://subclipse.tigris.org/files/documents/906/38385/site-1.2.3.zip，可以从这个地址下载Eclipse的插件，拷贝到plugins目录中以后，重启Eclipse就可以打开SVN的视图了。
也可以通过官方的 安装页面来进行在线安装：http://subclipse.tigris.org/install.html












