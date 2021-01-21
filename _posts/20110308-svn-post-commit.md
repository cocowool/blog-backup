---
title: 利用SVN的POST-COMMIT钩子自动部署代码
date: 2011-03-08 23:58:01
tag: 
keywords: svn, svn钩子, svn post-commit, svn钩子自动部署代码
description: 利用SVN的钩子，可以在代码提交的时候自动部署到目标服务器上。
---

最近配置了一台SVN服务器，用来保存自己一些小项目的代码。同时，SVN服务器也是一台前端的Web。所以希望利用SVN的POST-COMMIT钩子HOOKS，在提交代码的同时进行部署。

具体的步骤如下：

1、找到SVN项目的HOOKS目录。目录中默认会几个对应操作的钩子模板，我们需要创建一个post-commit的文件。

2、post-commit的主要内容

```sh
#/bin/bash
REPOS="$1"
REV="$2"
WEB_PATH="/home/fltrpsrv2/www"
TEST_PATH="/home/apache/testx/www.h.cn"

export LANG=zh_CN.UTF-8

CURDATE=`date`
echo "Code Deployed By at $CURDATE" >> /home/fltrpsrv2/svn/www.h.cn/hooks/code_deploy_log
/usr/local/bin/svn update --username xxx --password xxx /home/apache/htdocs/ >> /home/fltrpsrv2/svn/www.h.cn/hooks/
code_deploy_log
```

3、接下来需要我们首先Checkout一份代码到WEB的服务目录中。

4、代码CO出来之后，可以进行post-commit脚本的测试了。因为svn的hooks执行的时候不带有任何的环境变量，所以我们不能通过简单的 ./post-commit 进行代码的测试。必须要使用sudo su 等命令切换到svn或者apache服务器运行用户下，用下面的方法进行测试

> env – ./post-commit

这样，执行svn update 命令的用户就是svn或者apache服务器的运行用户，因此，我们需要保证代码的存放目录必须有对应用户的权限，否则，这个脚本是不能执行的。我就是因为这个权限的问题，耽误了很多时间。

关于权限问题，在SVN的官方站点中也有说明，大家可以参考后面的资料。

在Unix系统中，如果没有对应的用户，也可以使用c语言编写脚本的形式，并且赋予+S的权限来达到相应的目的，具体的查看附件中的资料吧。

补充，使用这个方法部署代码并不能够保证同步完成，执行 svn update 时，如果check出来的文件夹中存在和svn中重名的文件夹或者文件，会导致 update 失败，这一点需要注意。


参考资料：
1、[SVN Authentication and Auto Update](http://marcgrabanski.com/articles/svn-authentication-and-auto-update)
2、[SVN Forum](http://www.svnforum.org/threads/34952-post-commit-hooks-not-running.?p=103872&viewfull=1#post103872)
3、[Website auto update](http://subversion.apache.org/faq.html#website-auto-update)
4、[Hook Debuging](http://subversion.apache.org/faq.html#hook-debugging)












