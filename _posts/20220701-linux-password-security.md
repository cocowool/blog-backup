---
title: Linux 用户口令的安全策略
date: 2022-07-01 18:52:24
keywords: Linux, Linux 用户口令, Linux 口令安全策略, 安全, 口令安全
description: 
---

> 本文在 CentOS 7 上测试通过。

Linux 操作系统用户和口令是操作系统的重要信息，是操作系统安全的第一道防线，如果服务器暴露在互联网上允许 ssh 远程登录，则很容易被人尝试密码爆破，因此确保所有使用这台服务器的用户口令都符合安全的要求就非常重要。

系统管理员必须采用必要的技术手段强制用户使用强密码，并定期修改口令，防止用户口令被爆破猜解，保证系统用户账号和密码的安全。

Linux 操作系统所有的用户都存放在 `/etc/passwd` 文件中，首先要对 `/etc/passwd` 实行白名单管理，只有我们实际需要并且认可的用户才能长期存在，如果因为测试验证等原因，临时加的用户，我们务必做到及时清理。对于不需要远程登录，但确实需要保存在系统中的用户，可以将对应的用户登录 shell 设置为 `/sbin/nologin` 。

在 Linux 中身份认证等功能是由 PAM (Pluggable Authentication Modules) 模块来负责的，它是一套提供不同认证模块的共享库，保存在 /lib64/security 目录下。PAM 的配置文件存放在 `/etc/pam.d/` 路径下。

## 密码复杂度

人们设置密码的时候，很容易设置一些简单密码，如 password、123456、admin 之类，这些被称为弱口令，很容易被攻击者猜出来，因此我们需要对密码的复杂度做出要求，通常包括以下几个方面：

* 密码的长度，如最短长度不能少于 8 位
* 密码字符的种类，如至少需要包含小写字母、大写字母、数字、特殊符号
* 密码与用户名的相似度，如密码中不允许包含用户名
* 密码重复的次数，如不允许轮换使用 2 个密码，不允许和最近几次密码重复
* 密码错误允许的次数

CentOS 7 开始使用 pam_pwquality 模块进行密码复杂度策略管理，pam_pwquality 替换了 CentOS 6 中的 pam_cracklib 模块，并且向后兼容。pam_pwquality 的英文介绍是「PAM module to perform password quality checking」我理解主要是用来检查设置的密码质量，这个模块作用的英文描述如下：

> The action of this module is to prompt the user for a password and check its strength against a system dictionary and a set of rules for identifying poor choices.
>
> The first action is to prompt for a single password, check its strength and then, if it is considered strong, prompt for the password a second time (to verify that it was typed correctly on the first occasion). All being well, the password is passed on to subsequent modules to be installed as the new authentication token.

密码复杂度策略配置文件默认位于 `/etc/pam.d/system-auth` 默认配置如下：

```sh
password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
```

* tray_first_pass 主要用来防止用户新设定的密码和以前的旧密码相同
* retry=3 表示修改密码重试 3 次后报错退出密码修改界面（三次修改密码都不成功）
* Local_users_only 表示仅对 /etc/passwd 列表中的用户密码进行校验，这个选项默认是关闭的

也可以通过修改 `/etc/security/pwquality.conf` 配置文件来达到同样的效果。

> `/etc/pam.d/system-auth` 是一个非常重要的安全配置文件，文件的具体内容可以见下面的代码，四列的含义分别为：第一列代表模块类型、第二列代表控制标记、第三列代表模块路径、第四列代表模块参数。
>
> ```sh
> #%PAM-1.0
> # This file is auto-generated.
> # User changes will be destroyed the next time authconfig is run.
> auth        required      pam_env.so
> auth        required      pam_faildelay.so delay=2000000
> auth        sufficient    pam_unix.so nullok try_first_pass
> auth        requisite     pam_succeed_if.so uid >= 1000 quiet_success
> auth        required      pam_deny.so
> 
> account     required      pam_unix.so
> account     sufficient    pam_localuser.so
> account     sufficient    pam_succeed_if.so uid < 1000 quiet
> account     required      pam_permit.so
> 
> password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
> password    sufficient    pam_unix.so sha512 shadow nullok try_first_pass use_authtok
> password    required      pam_deny.so
> 
> session     optional      pam_keyinit.so revoke
> session     required      pam_limits.so
> -session     optional      pam_systemd.so
> session     [success=1 default=ignore] pam_succeed_if.so service in crond quiet use_uid
> session     required      pam_unix.so
> ```
>
> PAM 模块一共有四种类型，包括认证（auth）、账号（account）、会话（session）和密码（password），一个类型可以有多行，按照配置文件中顺序由 PAM 模块调用。

如果要增加密码的复杂度要求，我们可以将配置修改为

```sh
password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=  lcredit=-1 ucredit=-1 dcredit=-1 ocredit=-1 minlen=8 
```

新增加的参数表示：

* lcredit=-1 表示密码至少需要 1 位小写字母
* ucredit=-1 表示密码至少需要 1 位大写字母
* dcredit=-1 表示密码至少需要 1 位数字
* ocredit=-1 表示密码至少需要 1 位特殊字符
* minlen=8 表示密码的最短长度为 8 位

在 CentOS 6 中可以通过 `/etc/login.defs` 文件设置一些密码策略。

```sh
# 省去了很多注释，只关注具体内容
$ cat /etc/login.defs
# 密码允许使用的最大天数
PASS_MAX_DAYS	99999
# 两次密码修改之间允许的最小天数
PASS_MIN_DAYS	0
# 可接受的最小密码长度
PASS_MIN_LEN	5
# 密码过期前 x 天开始金星个密码过期提醒
PASS_WARN_AGE	7
```

## 密码过期策略

通过编辑 `/etc/login.defs` 可以设置 Linux 用户的密码策略，包括密码最小长度、密码最小更改周期、密码最大有效期、过期前警告天数等等，具体如下：

## 锁定策略

现在智能手机都有类似的锁定策略，例如密码输入错误 x 次后锁定 x 分钟。

## 其他安全措施

* 限制 su 的使用

## 模块列表

* pam_pwquality PAM module to perform password quality checking
* pam_pwhistory PAM module to remember last passwords
* Pam_passwdqc Password quality-control PAM module
* Pam_permit The promiscuous module
* Pam_pkcsll PAM Authentication Module for PKCS#11 token libraries
* pam_postgresok simple check of real UID and corresponding account name
* pam_access PAM module for logdaemon style login access control
* Pam_cap PAM module to set inheritable capabilities
* Pam_ck_connector Register session with ConsoleKit

## 后记

在整理这篇资料的时候，通过百度关键词搜索 pam_pwquality 几乎千篇一律的都是同一篇文章简单的复制粘贴，虽然我非常能理解大家这么做的初衷，一方面是为了自己笔记记录，另一方面也能够分享出来让更多人看到。可是对比 bing.com 搜索的结果，里面的内容质量就高很多了。希望今后中文技术博客世界能够更多一些认真的作者，不再仅仅是文字的搬运工。

## 参考资料

1. [Linux 用户口令安全策略](https://blog.csdn.net/weixin_36446632/article/details/116630545)
2. [CentOS 7 设置账号密码复杂度](https://blog.csdn.net/u013930899/article/details/125380021)
3. [CentOS 7 系统优化](https://blog.51cto.com/dongweizhen/3607450)
4. [pam_pwquality(8) Linux Man Page](https://www.systutorials.com/docs/linux/man/8-pam_pwquality/)
5. [Linux PAM 认证机制](http://bbs.learnfuture.com/topic/7346)
6. [Enforce strong passwords with passwdqc](https://www.techrepublic.com/article/enforce-strong-passwords-with-pam-passwdqc/)
7. [pam_passwdqc is not fully compatible on centos 7](https://github.com/dev-sec/chef-os-hardening/issues/102)
