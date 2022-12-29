---
title: Oracle常用SQL语句
date: 2021-04-14 17:44:12
tags:
keywords: oracle sql, sql, oracle, oracle常用SQL
description: 记录了工作中较常用到的SQL语句，备查。
---

> 本文SQL在Oracle 10g R2（10.2.0.5）下验证通过。

### 1. 使用系统管理员身份登录Oracle；

```sql
sqlplus / as sysdba
```

### 2. 查看UNDO表空间分区使用情况
```sql
select status,count(*),tablespace_name from dba_undo_extents group by tablespace_name,status;
```

查看表空间的大小
```sql
select status,count(*),tablespace_name,sum(bytes)/1024/1024 from dba_undo_extents group by tablespace_name,status;
```

查看UNDO表空间文件及大小
```sql
select file_name ,bytes/1024/1024 from dba_data_files where tablespace_name='UNDOTBS2';
```

### 3. 查看UNDO表空间参数

```sql
show parameter undo
```

### 4. 查看当前事务数量 

```sql
select count(*) from v$transaction;
```

### 5. 打开计时开关

```sql
set timing on
```
然后可以使用 show timing 查看设置是否生效

### 6. 数据泵导出数据文件
```sql
expdp jhitsm/password+0@jhitsm directory=ORADATA_DUMP2 dumpfile=20140711fulldb_01.dmp,20140711fulldb_02.dmp,20140711fulldb_03.dmp,20140711fulldb_04.dmp logfile=20140711fulldb_expdp_log.log schemas=jhitsm parallel=4
```

### 7. 查看表空间下有多少表，查看某个用户有多少表

```sql
select * from all_tables where tablespace_name='XXX';
select table_name from all_tables where tablespace_name='XXX';
select * from all_tables where tablespace_name='YYY' and owner='XXX';
```

### 8. 查看是否为RAC部署
```sql
SQL>   select instance_number,INSTANCE_NAME from gv$instance; 
```
如果是rac就是多行。这里显示是多行，说明是RAC。

```sql
select * from v$option a where a.PARAMETER='Real Application Clusters';
```

### 参考资料：
1. Oracle 10g Administration Study Guide
2. [oracle查看某个表空间下有多少表](https://blog.csdn.net/ysq5202121/article/details/45096487)