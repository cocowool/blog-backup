---
title: Vimperator的有用操作
date: 2008-09-21 16:50:01
tag: 
---



昨天使用了具有无敌魅力的Vimperator，今天接着来说一些常用的功能：


1. 搜索。
/something you want to search
这样就可以将页面中相关的内容高亮显示出来，n是下一个,N是上一个。
还可以支持正则。

2. 链接地址。
gu    到url的上一层目录；
gU    到url的根目录；

y/p    把当前的url复制到剪贴板或者在新的标签中打开
P    在新的标签页中打开粘贴板中的url

3. 页面。
<C-g>或者:pageinfo可以查看当前的页面信息；
gf或者:viewsource可以查看当前页面的源码；
zi/zo    放大或者缩小当前页面；
r或者：reload 能够重新加载页面；
<C-c>或者:stop停止加载当前页面；

4. 调用Firefox的各种属性。
：pref    调出firefox的preference对话框；
：pref！    调出firefox的about：config；
：addon    调出firefox的插件管理；

：dialog    addons    调出扩展管理、console    调出控制台、downloads调出下载列表。

5. 几个比较有用的命令。
：restart重启ff
：saves保存页面；
：set设置选项；
：！cmd可以执行系统命令；（PS：这个命令在我的Vista U下不幸挂掉了，我认为可能与UAC有关，所以想尝试这个命令的Vista用户，要三思）


可以有一个变通的办法复制，先按下"Ctrl+V"，然后再用快捷键复制，接下来Vim的快捷键又起作用了。现在还有一个困惑的地方是不用鼠标，如何选中网页中自己希望的文字并复制呢？












