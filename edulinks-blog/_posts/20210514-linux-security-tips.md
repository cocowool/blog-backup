---
title: 20个Linux服务器安全强化建议
date: 2021-05-14 18:38:54
tags:
keywords: Linux, Linux安全, Linux服务器安全
description: 本文基于CentOS、RHEL系统或者Ubuntu/Debian的发行版本介绍了20条Linux系统强化建议。
---

Linux服务器安全对于保护用户数据、知识产权非常重要，同时还能减少你面对黑客的时间。在工作中，通常由系统管理员对Linux的安全负责，在这篇文章中，介绍了20条对Linux系统进行强化的建议。本文所有的建议都基于CentOS、RHEL系统或者Ubuntu/Debian的发行版本。

## 1、加密数据通信方式。

所有通过网络传输的数据都是可以被监听的，因此只要有可能就要使用密码、证书等方式加密你的通讯数据。

1、使用 scp、ssh、rsync或者sftp来进行文件传输。也可以使用特殊的sshfs或者fuse工具来挂载远程文件系统或者你的工作目录。
2、GnuPG 提供功能丰富的证书管理功能，允许你签名数据并进行传输。
3、Fugu 是一个图形化的SFTP文件传输工具。SFTP类似于FTP，但是与FTP不同，整个会话是加密的，也就是说不会用明文形式发送密码。另外一个选项是FileZilla，一个跨平台的客户端段，也支持FTP、FTPS 和 SFTP。
4、OpenVPN 是一个轻量级、低成本的SSL VPN。
5、Lighttpd SSL（Secure Server Layer）Https 的安装和配置。
6、Apache SSL（Secure Server Layer）Https（mod_ssl）的安装和配置。

#1.1、避免使用FTP、Telnet和Rlogin/Rsh服务

在大多数的网络配置下，用户名、密码，FTP / telnet /rsh 命令和传输的文件能够被同网段的任何人使用包嗅探软件监听。这个问题的通常解决方法是使用 OpenSSH、SFTP 或者 FTPS。

下面的命令可以帮助你删除服务器中没必要的服务。
```sh
# yum erase inetd xinetd ypserv tfpt-server telnet-server rsh-server
```

## 2、最小化软件安装原则。

你确实需要服务器上安装的所有服务吗？避免安装不必要的服务就是避免漏斗。使用 RPM 包管理工具，例如 yum 或者 apt-get 、dpkg 来检查系统上安装的软件包，同时删除不必要的包。
```sh
# yum list installed
# yum list packageName
# yum remove packageName
```
或者
```sh
# dpkg --list
# dpkg --info packageName
# apt-get remove packageName
```

## 3、每个系统或实例上只运行一种服务。

将不同的服务运行在单独的服务器或虚拟化实例中。例如：如果黑客攻破Apache进入到系统中，他就可以访问部署在这台服务器上的Mysql、E-Mail等其他服务，尽量不要这么做。

## 4、保持Linux内核和软件的更新。

维护系统的一项重要工作就是及时的安装系统补丁。Linux提供了很多必要的工具和方法来保证系统的更新，所有安全方面的更新都应该尽快执行，与第2条一样，我们可以使用 yum、apt-get等工具来进行安全更新。
```sh
# yum update
```
或者
```sh
# apt-get update && apt-get upgrade
```
你可以在系统中配置更新提示邮件（Red hat、CentOS、Fedora），或者另外一个办法就是通过一个cron定时任务安装所有的安全更新。

## 5、使用Linux安全扩展。

Linux提供了多种安全补丁，可以用来保护错误配置或者一些妥协的方案。尽可能使用 SELinux和其他Linux安全扩展来加强网络和程序的限制。例如，SELinux提供了Linux内核的安全策略。

## 5.1、SELinux

SELinux提供了一套灵活的访问控制机制（MAC：Mandatory Access Control），标注的MAC下一个应用程序或者进程具有相关权限的用户下运行。使用MAC的内核保护能够使系统免于遭受系统的恶意攻击。更相信的信息可以查看官方的SELinux说明配置文档。

## 6、强密码策略。

当我们使用 useradd、usermod 命令创建或维护用户账号时，确保始终应用强密码策略。例如，一个好的密码至少包括8个字符，包含了字母、数字以及特殊字符串、大小写等。使用诸如“John the ripper”这样的工具来查找弱口令用户，使用 pam_cracklib.so 来增强密码策略。

## 6.1 密码生命周期。

chage 命令能够修改口令的修改周期，以及最近一次密码修改的日期。系统依据这些信息判断用户口令是否应该修改。在 /etc/login.defs 文件中定义了包括密码生命周期在内的一些选项。如果需要对某个用户禁用密码生命周期，如下：
```sh
# chage -M 99999 userName
```
获取密码过期信息，输入：
```sh
chage -l userName
```
我们也可以在 /etc/shadow 文件中定义这些字段：

{userName}:{password}:{lastpasswdchanged}:{Minimum_days}:{Maximum_days}:{Warn}:{Inactive}:{Expire}:

其中：Minimum_days 定义密码修改的最小时间间隔，也就是用户能够修改密码的最小时间间隔。Maximum_days 定义密码有效的时间间隔，超过这个时间，用户就必须修改密码。Warn 定义口令过期多少天前开始向用户提示进行口令修改。Expire 定义从1970年1月1日到过期时的天数，之后该用户将无法再登录。

建议使用 chage 命令，而不是修改 /etc/shadow 文件
```sh
# chage -M 60 -m 7 -W 7 userName
```
## 6.2、禁止使用之前用过的密码。

可以设置禁止所有用户使用之前的旧密码，pam_unix 模块提供了这个功能，允许我们定义之前几个旧密码不能使用。

## 6.3、登录失败后锁定用户。

在Linux中可以使用 faillog 命令来显示失败的登录或者设置失败登录限制。查看失败的登录，可以输入：

faillog

解锁登录失败的用户，运行

faillog -r -u userName

注意可以使用 passwd 命令来锁定或解锁用户密码。

## 6.4、如何来检查是否有账号使用了空口令。

使用如下命令：
```sh
# awk -F: '($2 == "") {print}' /etc/shadwo
```
锁定所有空密码的账户
```
# passwd -l accountName
```
## 6.5、确保没有非Root用户的UID为0。

只有Root用户的UID为0，其具有系统的所有权限。使用下面的命令进行检查：
```sh
# awk -F: '($3 == "0") {print}' /etc/passwd
```
应该仅能看到root一行的结果，如果还有其他用户，请将这些用户删除。

## 7、禁止root用户登录。

永远不要使用root用户登录，应该使用 sudo 来执行需要root权限的命令。sudo 避免了root口令的共享，同时提供了一些审计和追踪的功能支持。

## 8、服务器的物理安全。

我们必须确保服务器的物理安全，配置 BIOS 禁止从外部设备启动。设置 BIOS 和 grub boot loader 的密码。所有的设备应当安全的存放在IDC（Internet Data Center）中，并且安排了适当的机房安检。

## 9、禁用不需要的服务。

禁用所有不必要的服务和守护进程，并且将他们从随系统启动中删除。使用下面的命令来检查是否有服务随系统启动。
```sh
# chkconfig --list | grep '3:on'
```
要禁用服务，可以使用下面的命令：
```sh
# service serviceName stop
# chkconfig serviceName off
```
## 9.1、检查网络监听的端口。

使用 netstat 命令查看服务器中有哪些监听端口
```sh
# netstat -tulpn
```
如果有不需要的服务，可以使用 chkconfig 进行关闭。如果需要对外屏蔽，可以使用 iptables 。

## 10、删除X Windows。

对服务器来说，X Windows完全没有必要。可以使用包管理工具删除。
```sh
# yum groupremove "X Window System"
```
## 11、配置iptables和TCPWrappers。

iptables 是一个Linux内核提供的，运行在用户空间的程序，它允许用户配置自己的防火墙策略。我们可以使用防火墙将不必要的流量过滤出去。使用 iptables 能够避免很多拒绝服务（DoS）攻击。

## 12、内核配置文件优化（/etc/sysctl.conf）。

/etc/sysctl.conf 文件用来修改服务器的运行时内核参数配置。Linux在启动时从配置文件中读取相关配置，一般内容如下：
```sh
# Turn on execshield
kernel.exec-shield=1
kernel.randomize_va_space=1
# Enable IP spoofing protection
net.ipv4.conf.all.rp_filter=1
# Disable IP source routing
net.ipv4.conf.all.accept_source_route=0
# Ignoring broadcasts request
net.ipv4.icmp_echo_ignore_broadcasts=1
net.ipv4.icmp_ignore_bogus_error_messages=1
# Make sure spoofed packets get logged
net.ipv4.conf.all.log_martians = 1
```
## 13、合理安排磁盘分区。

将不同的系统文件分配到不同的磁盘分区上有助于构建一个安全的系统，确保下面这些文件目录都被挂在到单独的磁盘分区上。
```sh
/usr
/home
/var、/var/tmp
/tmp
```
为Apache和FTP服务的根目录创建单独的分区，检查 /etc/fstab 文件确保增加了以下配置

noexec:该分区不允许执行任何二进制文件（但是允许脚本运行）。
nodev:该分区不允许字符设备或特殊设备。
nosuid:该分区不允许设置SUID/SGID。

下面的例子限制用户进入到 /dev/sda5 中。

/dev/sda5 /ftpdata ext3 defaults,nosuid,nodev,noexec 1 2

## 13.1、磁盘配额。

确保对所有用户都启用了磁盘配额限制，实现磁盘配额的具体步骤可以查看这里。

## 14、关闭IPv6。

IPv6提供替代IPv4的新的TCP/IP协议，但是目前还没有太好的工具来检查IPv6网络的安全性。大多数Linux发行版开始默认使用IPv6协议。而黑客可以利用IPv6网络来发送一些管理员无法监测的内容，除非是网络配置需要，否则禁止使用IPv6。

## 15、禁用不需要的SUID和SGID二进制。

所有启用了SUID和SGID位的二进制文件可能会造成系统的安全问题或者BUG，从而让所有本地或远程的用户使用这些文件。下面的命令可以找到这些文件
```sh
#See all set user id files:
find / -perm +4000
#See all group id files:
find / -perm +2000
#或者一起查询
find / \(-perm -4000 -o -perm -2000 \) -print
find / -path -prune -o -type f -perm +6000 -ls
```
## 15.1、World-Writable文件。

因为安全原因，所有人都可以修改World-Writable文件，使用下面的命令可以找到所有此类文件。
```sh
# find /dir -xdev -type d \( -perm -0002 -a ! -perm -1000 \) -print
```

## 15.2、无属主文件。

如果一个文件没有任何的用户或者组权限，那么可能会造成安全问题，使用下面的问题来找到此类文件，并进行后续的修复。
```sh
# find /dir -xdev \( -nouser -o -nogroup \) -print
```

## 16、使用集中的授权服务。

如果没有集中的授权认证系统，用户的权限数据将会变得不完成，从而导致一些过期的口令或者不用的用户一直保留下来。集中地授权认证服务允许我们控制Linux/Unix用户和授权数据，不要使用 NIS 服务，OpenLDAP是一个很好的选择。

## 17、日志和审计。

为了收集黑客攻击行为，我们需要配置系统的日志和审计功能。默认情况下，syslog在 /var/log 目录下记录数据。这些数据也能帮助我们发现那些容易导致系统遭受攻击的错误配置，可以参考 这个文章 来了解更多的内容。

## 17.1、使用Logwatch 或 Logcheck 监控可疑的日志信息。

使用 Logwatch 或者 Logchek 读取日志文件，这些工具改善了阅读日志文件的体验。他们会将结果以邮件的形式进行报告，而我们只需要检查日志中的不常见项目就可以了。

## 17.2、使用 auditd 服务。

auditd是系统提供的审计服务，负责将审计数据记录到磁盘中。在服务器启动期间，/et/audit/audit.rules的规则会被应用。我们修改配置文件来定义日志存放位置以及其他的选项。使用这个服务可以解决以下问题：

1、系统启动和关闭时间（reboot/halt）
2、事件的日期和时间
3、事件用户
4、事件类型
5、事件是否成功
6、记录事件发生变化的日期和时间
7、找出修改系统网络配置的账户
8、记录修改用户/用户组信息的事件
9、记录文件修改信息

具体可以查看这篇文章了解更多内容。

## 18、加固 OpenSSH 服务器。

SSH协议经常用来进行远程登录和文件传输，同时SSH也是暴露在外的，使用以下方法可以对OpenSSH服务器进行加固。


## 19、安装和使用入侵检测系统。

网络入侵检测系统可以检测到恶意的活动或者拒绝服务共计、端口扫描等类似的黑客行为。最好在系统上线前能够部署一套类似的系统，如果可能在系统接入网络前就安装配置好。AIDE就是一个非常好的例子。Snort也可以用来做实时的网络流量分析和入侵检测。

## 20、保护文件、目录和邮件。

Linux系统提供了非常好的非授权的数据访问保护机制。文件权限和MAC确保数据不会被未授权的用户访问。但是，如果黑客具有物理的访问途径，那他还是能够很容易通过其他方式访问磁盘上的数据。我们可以使用将文件进行加密、使用密码、加密文件夹等方式来保护我们的重要数据。

参考资料：
1、http://www.cyberciti.biz/tips/linux-security.html