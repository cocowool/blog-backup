---
title: 用Git导出项目
date: 2015-05-11 22:58:01
tag: 
keywords: git, git导出
description: Git提供了archive命令，可以把版本的文件流导出。
---

Git没有SVN的导出功能，不能像 svn export url 那样，将某个版本的代码导出为不带版本控制文件的文件夹。

Git提供了archive命令，可以把版本的文件流导出。

可以将Git的特征值加入到导出的文件名中，例如：

```sh
git archive --format zip -o site-$(git log --pretty=format:"%h" -1).zip HEAD
```

对于打过tag的历史版本，我们可以使用tag导出，例如：

```sh
git archive v1.0 | gzip > xxxx.tgz
```

参考资料：
1、[用Git导出项目](http://marshal.easymorse.com/archives/4584)
2、[Git如何导出历史版本](http://www.v2ex.com/t/47145)