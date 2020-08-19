---
title: Fedora 11 的安装以及 LAMP环境的搭建(二)
date: 2009-09-22 09:46:01
tag: 
---


补充：首先，补充上一篇中的一些常用软件

(8)、RAR支持

Windows下的同事，很多都使用winrar来压缩和传输文件，所以这个也是不可或缺的，提供对于rar压缩格式的支持
sudo yum install unrar

(9)、Filezilla，FTP支持

Linux下的优秀的ftp客户端。
sudo yum install fillzilla

**五、LAMP开发环境配置**

(1)、ZendStudio 6.1.2

Zend Studio 的安装非常方便，从官方下载 ZendStudioForEclipse_6_1_2.bin 后直接在命令行下执行就可以出现图形的安装界面，选择好后一切就OK了。

项目的代码使用SVN进行管理，所以需要从SVN中check项目：
google SVN 密码：xxxxx
google SVN 地址：
phpplot        https://xxxxx.googlecode.com/svn/trunk/

(2)、Virtualbox

到官方下载RPM安装包    VirtualBox-3.0.6_52128_fedora11-1.i586.rpm    ，安装很简单 rpm -ivh Vir** 就可以了。
之后就可以建立自己的虚拟机。

(3)、Apache安装和配置

Apache 的安装通过yum很简单，执行命令：sudo yum install apache 就可以了。
服务启动命令：sudo service httpd start
不用service的话，命令都在：/usr/sbin/ 下

Apache 默认配置文件在 /httpd/conf/httpd.conf，可以在这个文件中做一些基本的配置定义。
配置虚拟主机，apache 在配置文件中设定了启动包含 /etc/httpd/conf.d/ 中的配置文件，所以如果建立虚拟机，我们只需要在 /etc/httpd/conf.d/vhost.conf 中进行编辑即可，而不需要编辑默认配置文件。
典型的虚拟主机配置如下：
#www.work.rk.cn
NameVirtualHost *:80
<VirtualHost *:80>
ServerAdmin cocowool@gmail.com
DocumentRoot /home/cipher/Zend/workspaces/DefaultWorkspace/
ServerName www.work.cn
ErrorLog logs/www.work.cn-error_log
CustomLog logs/www.work.cn-access_log common

SetEnv SRV_MEMCACHED_KEY_PREFIX     'work_cn'
SetEnv SRV_MEMCACHED_HOST           'localhost'
SetEnv SRV_MEMCACHED_SERVERS        '127.0.0.1:11211'
SetEnv SRV_MEMCACHED_PORT           '11211'

<Directory "/home/cipher/Zend/workspaces/cgi">
AllowOverride Options
Allow from All
</Directory>
</VirtualHost>

注意的问题：
NameVirtualHost 在虚拟主机的配置文件中，只需要写一份就可以了，否则会报出一个 warnning；
如果 DocumentRoot 设置在自己的用户目录下，那么很可能会遇到 访问被拒绝的(403) 的问题，这个时候需要修改用户目录的权限，例如：
chmod 711 -R /home/cipher/
同时还需要检查在几的 Selinux 是否打开，如果是本地的测试开发环境，建议关闭，图形界面下选择 System -> Administration -> SELinux Management，然后 Disable 掉就可以了。如果需要的话，可以使用：
setseool -P httpd_enbale_homedirs 1
chcon -R -t httpd_sys_content_t /home/cipher/workspace
单独对虚拟主机的目录进行权限设置。

(4)、PHP的安装及配置

sudo yum install php
fedora 11源中提供的PHP版本为 php 5.2.9，这个版本应该来说还是比较稳定，在 Fedora 12 中将会使用 php 5.3，很多人不推荐使用这个版本，服务器上一般会采用比较稳定的 php 5.2.5 或者 php 5.2.6；

PHP的默认配置文件位于 /etc/php.ini，我经常需要的几个配置项有：
配置 include_path，将自己常用的一些库文件，包括 PEAR，SMARTY 类文件放在一起；
配置 error_report，关闭 notice，个别项目需要的话，可以在项目中打开；

添加Memcache支持：sudo yum install php-pecl-memcache
添加mysql支持：sudo yum install php-mysql.i586

其他可能会遇到的支持包括：GD，sqlite，PDO，mysqli，ftp，ldap，xmlrpc，zlib，bz2，png，mhash，iconv，mcrypt，simplexml，json，dom，mbstring，soap，curl等
这些基本上我是用到发现没有的时候就去添加，当然这个习惯很不好，应当制定一套基本的服务器环境规范，每个项目都遵守。

(5)、Mysql的安装及配置

Fedora 11 的 yum 源中提供的 mysql 版本为 5.1.37
mysql的安装：sudo yum install mysql
启动命令：sudo service mysqld satrt
mysql 的默认配置文件位于 /etc/my.cnf ，另外还有5个推荐的配置文件位于 sudo vi /usr/share/doc/mysql-server-5.1.37/ 分别适用于不同数据量的情况，可以酌情选用 ；

等到装Mysql的时候才想起来，之前系统中的数据都没有备份出来，很是可惜。在Windows下备份的时候，一般选择将数据文件拷贝一份就可以了，而在 Fedora 下，除了 mysqldump 和 mysqlbinlog 外，亦可以将数据文件备份下来，下面介绍具体方法。
首先停掉系统中的 mysql 服务：sudo service mysqld stop
移动数据文件到自己的目录下：sudo mv -f mysql/ ~/Data/，这里要注意权限
修改 /etc/my.cnf 中的相关配置为实际情况：
[mysqld]
#datadir=/var/lib/mysql
#socket=/var/lib/mysql/mysql.sock

datadir=/home/cipher/Data/mysql
socket=/home/cipher/Data/mysql/mysql.sock
修改启动脚本 /etc/init.d/mysqld ：
#get_mysql_option mysqld datadir "/var/lib/mysql"
get_mysql_option mysqld datadir "/home/cipher/Data/mysql"
重新启动 mysql 服务： sudo service mysqld start
使用命令：mysql -uroot --sock=/home/cipher/Data/mysql/mysql.sock 便可以进入数据库，创建一个测试的数据库 overtest，之后便可以在 /home/cipher/Data/mysql 下看到新建立了一个数据文件夹。
这个时候，如果直接使用 mysql -uroot 进行登录，会提示：Can't connect to local MySQL server through socket '/var/lib/mysql/mysql.sock'，原因当然是那个位置已经没有这个文件了
为了改正这个错误，再次编辑 /etc/my.cnf，添加：
[client]
socket=/home/cipher/Data/mysql/mysql.sock
重启服务后，再次使用 mysql -uroot 登录，OK！

(6)、其他所需组件的安装及配置

Memcached，

(7)、设置服务随机启动

sudo ntsysv，命令执行后，会出现一个图形界面，用空格选择需要随机启动的服务，然后点击OK就可以了。

**六、系统性能的优化配置**

(0)、启动问题

使用一段时间后，发现系统启动的时候 sendmail 和 sm-client 这两个服务启动的特别慢，google 了一下原因，原来是在 /etc/hosts 中添加了本机地址和对应的主机名记录，去掉以后，启动很快（不过貌似超过了宣传的20秒）。
现在有一点事情想不明白，刚装上系统的时候，使用 sudo 命令编辑执行非常缓慢，所以在 /etc/hosts 中添加了一条记录，现在又造成了启动变慢，去掉之后，在执行 sudo 却并没有影响，不知道是不是后来安装的一些包中，解决了这个问题。

(1)、Firefox的内存占用问题

修改内存占用，在地址栏输入 about:config
修改如下选项：
browser.cache.memory.capacity = 1024
browser.sessionhistory.max_total_viewers = 1

修改Firefox选项：
Privacy -> History -> 选择 Use Custom settings for History
修改 Remeber my browsering history for at least 15 days ，根据自己的习惯选吧，不要太大；
勾选 Clear history when firefox closes -> 然后再适当的选择需要删除的历史；


(2)、ZendStudio 6.1.2 优化

增加内存；
关闭 Code Analyzer；
关闭其它不相关的项目；

如果不喜欢用 Zend 自带的 JRE 的话，可以参考[4]来进行 JRE 环境的替换，这样据说能够提供一个比较漂亮的 Zend 的日志记录。

(3)、Vimperator 的优化

我们可以在home下创建 .vimperatorrc 来定义自己喜欢的快捷键，而不是用vimperator默认提供的。
我的配置文件如下：

"---------------------------
"Common setting
"---------------------------

" 先把麻烦的东西拿掉
map A <Nop>
map <C-q> <Nop>
map <C-o> <Nop>
map <C-i> <Nop>
map <C-z> <Nop>
map <C-p> <Nop>

"set nocompatible
set clipboard+=unnamed

"display tool bar
set go+=T

"Next Tab
noremap <Left> gT

"Prev Tab
noremap <Right> gt

noremap q :back<CR>
noremap w :forward<CR>

" 解决全选、复制、粘帖、剪切和撤销与vimperator冲突的问题
noremap <C-V> <C-v>
noremap <C-Z> <C-z>
noremap <C-c> <C-v><C-c>
noremap <C-a> <C-v><C-a>
cnoremap <C-c> <C-v><C-c>
cnoremap <C-v> <C-v><C-v>
cnoremap <C-x> <C-v><C-x>
inoremap <C-a> <C-v><C-a>
inoremap <C-c> <C-v><C-c>
inoremap <C-v> <C-v><C-v>
inoremap <C-x> <C-v><C-x>
inoremap <C-z> <C-v><C-z>
inoremap <C-y> <C-v><C-y>

参考资料:
1、[Fedora 下 Apache 的配置](http://www.linuxidc.com/Linux/2009-02/18262.htm)
2、[Zend 优化](http://www.zend.com/support/knowledgebase.php?kbid=319)
3、[优化Firefox的内存占用](http://www.linuxsir.org/bbs/thread275230-2.html)
4、[替换 Zend 的 JRE](http://www.zend.com/support/knowledgebase.php?kbid=353)
5、[Mysql 的安装配置](http://www.linuxidc.com/Linux/2009-01/18097.htm)
6、[Fedora10下Mysql的安装配置](http://www.cnblogs.com/songQQ/archive/2009/07/13/1522379.html)
7、[修改Mysql数据位置](http://www.wangchao.net.cn/bbsdetail_1632534.html)
8、[Mysql 启动错误解决](http://hi.baidu.com/dwj192/blog/item/b656991be00ff3138618bfc9.html)
9、[Vimperator配置](http://www.chinadforce.com/viewthread.php?tid=1204824&extra=page%3D4)
10、[试玩Vimperator](http://swdpress.cn/tag/vimperator/)











