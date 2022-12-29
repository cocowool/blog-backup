---
title: Oracle重置过期的密码
date: 2014-03-10 22:33:01
tag: 
keywords: oracle, oracle linux, oracle重置密码, oracle密码过期
description: 
---

Oracle重置过期的密码

过期的原因一般有两种可能：
一、由于Oracle 11g在默认的default概要文件中设置了“PASSWORD_LIFE_TIME=180”天导致；

这种情况的解决办法：
1、查看用户的proifle是哪个，一般是default：

```sql
sql>SELECT username,PROFILE FROM dba_users;
```

2、查看指定概要文件（如default）的密码有效期设置：

```sql
sql>SELECT * FROM dba_profiles s WHERE s.profile='DEFAULT' AND resource_name='PASSWORD_LIFE_TIME';
```

3、将密码有效期由默认的180天修改成“无限制”：
```sql
sql>ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
```

修改之后不需要重启动数据库，会立即生效。
4、修改后，还没有被提示ORA-28002警告的帐户不会再碰到同样的提示；
已经被提示的帐户必须再改一次密码，举例如下：
```sql
$sqlplus / as sysdba
sql> alter user smsc identified by <原来的密码> ----不用换新密码
```

oracle11g启动参数resource_limit无论设置为false还是true，密码有效期都是生效的，所以必须通过以上方式进行修改。以上的帐户名请根据实际使用的帐户名更改。

二、由于Oracle 11g在默认的default概要文件中设置了“FAILED_LOGIN_ATTEMPTS＝10”次所导致，当输入密码错误次数达到设置值将导致此问题。

1、查看用户的proifle是哪个，一般是default：
```sql
sql>SELECT username,PROFILE FROM dba_users;
```

2、查看指定概要文件（如default）的密码有效期设置：
```sql
sql>SELECT * FROM dba_profiles s WHERE s.profile='DEFAULT' AND resource_name='FAILED_LOGIN_ATTEMPTS';
```

3、将尝试登录失败次数由默认的10次修改成“无限制”：
```sql
sql>ALTER PROFILE DEFAULT LIMIT FAILED_LOGIN_ATTEMPTS UNLIMITED;
```

4、修改后，还没有被提示ORA-28000警告的用户不会再碰到同样的提示；
已经被锁定的帐户必须解除锁定，举例如下：
```sql
$sqlplus / as sysdba
sql> alter user smsc identified by oracle account unlock;
```

5、修改后default profile应该如下：
```sql
sql> select * from dba_profiles WHERE dba_profiles.profile='DEFAULT';
PROFILE RESOURCE_NAME RESOURCE_TYPE LIMIT
------------------------------ -------------------------------- ------------- ----------------------
DEFAULT FAILED_LOGIN_ATTEMPTS PASSWORD UNLIMITED
DEFAULT PASSWORD_LIFE_TIME PASSWORD UNLIMITED
```

修改之后不需要重启动数据库，会立即生效。
如果出现ORA-28000 the account is locked.错误
alter user 用户名 account unlock;

参考资料：
1、[Oracle 11g存在密码过期的问题](http://soft.chinabyte.com/database/43/11835543.shtml)
2、[Oracle 11g密码设置为不过期](http://blog.itpub.net/14184018/viewspace-750452)