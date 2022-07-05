---
title: Linux 用户口令的安全策略
date: 2022-07-01 18:52:24
keywords: Linux, Linux 用户口令, Linux 口令安全策略, 安全, 口令安全
---

> 本文在 CentOS 7 上测试通过。

Linux 操作系统用户和口令是操作系统的重要信息，是操作系统安全的第一道防线，如果服务器暴露在互联网上允许 ssh 远程登录，则很容易被人尝试密码爆破，因此确保所有使用这台服务器的用户口令都符合安全的要求就非常重要。

系统管理员必须采用必要的技术手段强制用户使用强密码，并定期修改口令，防止用户口令被爆破猜解，保证系统用户账号和密码的安全。

Linux 操作系统所有的用户都存放在 `/etc/passwd` 文件中，首先要对 `/etc/passwd` 实行白名单管理，只有我们实际需要并且认可的用户才能长期存在，如果因为测试验证等原因，临时加的用户，我们务必做到及时清理。对于不需要远程登录，但确实需要保存在系统中的用户，可以将对应的用户登录 shell 设置为 `/sbin/nologin` 。

## 密码复杂度

人们设置密码的时候，很容易设置一些简单密码，如 password、123456、admin 之类，这些被称为弱口令，很容易被攻击者猜出来，因此我们需要对密码的复杂度做出要求，通常包括以下几个方面：

* 密码的长度，如最短长度不能少于 8 位
* 密码字符的种类，如至少需要包含小写字母、大写字母、数字、特殊符号
* 密码与用户名的相似度，如密码中不允许包含用户名
* 密码重复的次数，如不允许轮换使用 2 个密码，不允许和最近几次密码重复
* 密码错误允许的次数

> CentOS 7 开始使用 pam_pwquality 模块进行密码复杂度策略管理，pam_pwquality 替换了 CentOS 6 中的 pam_cracklib 模块，并且向后兼容。

密码复杂度策略配置文件默认位于 `/etc/pam.d/system-auth` 默认配置如下：

```sh
password    requisite     pam_pwquality.so try_first_pass local_users_only retry=3 authtok_type=
```

* tray_first_pass 主要用来防止用户新设定的密码和以前的旧密码相同
* retry=3 表示修改密码重试 3 次后报错退出密码修改界面（三次修改密码都不成功）
* 




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

| 用户      | 密码    |
| --------- | ------- |
| testuser1 | haiyang |
| testuser2 | wang    |
|           |         |



## 密码过期策略

通过编辑 `/etc/login.defs` 可以设置 Linux 用户的密码策略，包括密码最小长度、密码最小更改周期、密码最大有效期、过期前警告天数等等，具体如下：

## 锁定策略

现在智能手机都有类似的锁定策略，例如密码输入错误 x 次后锁定 x 分钟。

## 其他安全措施

* 限制 su 的使用
* 

## 参考资料

1. [Linux 用户口令安全策略](https://blog.csdn.net/weixin_36446632/article/details/116630545)
1. [CentOS 7 设置账号密码复杂度](https://blog.csdn.net/u013930899/article/details/125380021)
1. [CentOS 7 系统优化](https://blog.51cto.com/dongweizhen/3607450)
