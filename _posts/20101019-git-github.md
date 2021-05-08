---
title: git与github在ubuntu下的使用
date: 2010-10-19 18:29:01
tag: 
keywords: git, GitHub, git ubuntu
description: 介绍下Ubuntu环境中使用git和github的方法。
---

最近开始使用git对kohana3的文档做一些补充的工作，使用了git 和 github ，从了解到使用，还是有一点距离，下面是总结的一些方法。

1、Git的安装
我使用了ubuntu 10.04 ，默认情况下，ubuntu 中并没有安装，所以首先需要在系统中进行 git 的安装。
`sudo apt-get install git-core` 安装完成后，在终端中输入 git 就可以看到相关的命令了。如果只是需要使用git来管理本地的代码，那么现在就可以使用了。如果需要和github上的项目结合，还需要做其他的一些操作。

![](/20101019-git-github/Screenshot.png)

2、github帐号的申请

如果只是需要将github上感兴趣的代码拷贝到本地，自己进行修改使用，而不打算共享发布的话，其实不申请帐号也没有关系，只需要 git clone 代码到本地就可以了。本文对这种方法不做讨论，毕竟使用 github 就是为了开源的目的。
首先去 github.com 上注册一个帐号，具体的注册流程就不赘述了。

3、在本地建立一个文件夹，然后做一些全局变量的初始化

```sh
git config --global user.name = "用户名或者用户ID"
git config --global user.email = "邮箱"
```
这两个选项会在以后的使用过程中自动添加到代码中。

4、创建验证用的公钥

这个是比较复杂和困扰大多数人的地方，因为 git 是通过 ssh 的方式访问资源库的，所以需要在本地创建验证用的文件。
使用命令：ssh-keygen -C 'you email address@gmail.com' -t rsa
会在用户目录 ~/.ssh/ 下建立相应的密钥文件
可以使用 ssh -v git@github.com 命令来测试链接是否畅通

5、上传公钥

在 github.com 的界面中 选择右上角的 Account Settings，然后选择 SSH Public Keys ，选择新加。Title 可以随便命名，Key 的内容拷贝自 ~/.ssh/id_rsa.pub 中的内容，完成后，可以再使用 ssh -v git@github.com 进行测试。看到下面的信息表示验证成功。

![](/20101019-git-github/Screenshot-1.png)

进行到这里，我们也可以利用github来管理我们的项目和参与开源工作了，大体上使用git分为三种方式。

**管理自己的项目**

这种情况是自己在本地有一些代码，需要利用 github 来管理自己的项目，可以按照下面的步骤进行。

1、建立仓库

在需要建立项目的文件夹中，使用 git init 进行仓库的建立。完成后，可以看到文件家中多了一个 .git 隐藏目录。

2、添加文件

使用 git add . 来进行初始文件的添加。这里 . 表示将文件夹下所有的文件都添加进去，我们也可以指定文件进行添加。

3、提交文件

使用 git -m commit  -m 'comment' 提交，可以将编辑的内容进行提交。
通过 git show 可以看到项目的一些状态。
这是代码已经在本地管理了，但是本地管理完全体现不出git的优势。

**利用github托管自己的项目**

1、如果需要将自己的项目托管在github.com上，首先需要创建项目。在 Dashboard 中选择 Create New Repository，填入相关信息后，项目就创建成功了。

2、之后，会有一个如何上传的提示，如下图已经很详细了。

![](/20101019-git-github/Screenshot.png)

**Fork别人的项目**

前面提到过，如果只是下载使用别人的代码，通过 git clone 就可以了，当然clone的前提是项目必须是public的，私有的项目是没办法的。如果需要参与，可以通过fork，然后合并的方式。

1、首先需要去自己感兴趣的项目中进行Fork，fork出自己的一份分支来。fork之后，一般可以看到一个ssh访问的地址。例如：git@github.com:cocowool/userguide.git

2、复制代码
使用 git clone git@github.com:cocowool/userguide.git userguide 可以将代码复制到本地的 userguide 文件夹中

3、进行自己的修改，完成后请求原作者合并

```git pull person master```

下面是一些比较有用的命令的介绍：
```sh
# 创建一个版本库
git init
# 每次修改好了后，可以先将修改存入stage(快照/索引)中
git add<modified files>
# 修改了大量文件则使用下面这个命令批量存入
git add .
# 使用commit将快照/索引中的内容提交到版本库中
git commit -m "msg"
# 也可以将git add与git commit用一个指令完成
git commit -a -m "msg"
# 将本地的git档案与github(远程)上的同步
git push
# 将github(远程)的git档案与本地的同步(即更新本地端的repo)
git pull
# 例如,pull指令其实包含了fetch(將变更复制回來)以及merge(合并)操作
git pull git://github.com/tom/test.git
# 另外版本控制系統的branch功能也很有意思，若同时修改bug，又要加入新功能，可以fork出一个branch：一个专门修bug，一个专门加入新功能，等到稳定后再merge合并
git branch bug_fix
# 建立branch，名为bug_fix 
git checkout bug_fix
# 切换到bug_fix
git checkout master
#切换到主要的repo
git merge bug_fix
#把bug_fix这个branch和现在的branch合并
# 若有remote的branch，想要查看并checkout
git branch -r
# 查看远程branch
git checkout-bbug_fix_local bug_fix_remote
#把本地端切换为远程的bug_fix_remote branch并命名为bug_fix_local
# 还有其它可以查看repo状态的工具
git log
#可以查看每次commit的改变
git diff
#可以查看最近一次改变的內容，加上参数可以看其它的改变并互相比较
git show
#可以看某次的变更
# 若想知道目前工作树的状态，可以輸入
git status
```

参考资料：
1、[Git与Github在Windows环境下的使用指南](http://blog.itmem.com/archives/1157)
2、[使用GIT管理源代码](http://www.ibm.com/developerworks/cn/linux/l-git/)
3、[git/github使用小记](http://arthraim.cn/git-github-usage/)
4、[使用git与github管理开发](http://gooss.org/the-use-of-git-and-github-management-development/)
