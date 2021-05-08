---
title: 修改服务的运行权限，解决SVN Post Commit问题
date: 2017-12-04 17:46:01
tag: 
keywords: svn, svn commit, svn post commit
description: 在Windows Server中安装了VirtualSVN Server，然后设置了Post Commit Hooks，在提交代码到版本库之后，将代码check到本地的一个Server目录。
---

先来看一下错误现象
![](/20171204-svn-commit/39469-20171204174550060-1733925531.png)
之前在Windows Server中安装了VirtualSVN Server，然后设置了Post Commit Hooks，在提交代码到版本库之后，将代码check到本地的一个Server目录。遇到这三个错误，首先在服务器上手工执行了一下post-commit.cmd脚本，可以执行没问题啊，偏偏从客户端提交代码，就遇到这个错误。
由此想到可能是权限的问题，任务管理器里查看服务，发现是“网络服务”用户组，可以在服务里修改一下权限。修改完成后记得重启一下服务，再试一下OK了。登录的用户只要是管理员组中的用户就可以，不一定必须是Administrator。
![](/20171204-svn-commit/39469-20171204174603497-607265840.png)
参考资料：
1、[TortoiseSVN 提交时 hook 的 post-commit.bat 不执行的解决方法](http://blog.csdn.net/leeyongit/article/details/28874133)
2、[通过VisualSVN的POST-COMMIT钩子自动部署代码](http://blog.csdn.net/sinat_30999181/article/details/48208987)












