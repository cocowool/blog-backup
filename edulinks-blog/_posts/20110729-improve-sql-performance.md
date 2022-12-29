---
title: 提高Mysql大表查询语句性能
date: 2011-07-29 18:52:02
tags:
keywords: mysql, mysql select, mysql 查询, mysql 大表查询, mysql 查询性能, mysql 大表查询性能, mysql 性能优化
description: 本文介绍对于大表查询，如何修改sql语句获得更好的执行性能。
---

有个几千万条记录的表 on MySQL 5.0.x，现在要读出其中几十万万条左右的记录。常用方法，依次循环：

```sql
select * from mytable where index_col = xxx limit offset, limit;
```

**经验**：如果没有blob/text字段，单行记录比较小，可以把 limit 设大点，会加快速度

**问题**：头几万条读取很快，但是速度呈线性下降，同时 mysql server cpu 99%

速度不可接受。调用 
```sql
explain select * from mytable where index_col = xxx limit offset, limit;
```

显示 type = ALL，在 MySQL optimization 的文档写到"All"的解释

> A full table scan is done for each combination of rows from the previous tables. This is normally not good if the table is the first table not marked const, and usually very bad in all other cases. Normally, you can avoid ALL by adding indexes that allow row retrieval from the table based on constant values or column values from earlier tables.

看样子对于 all, mysql 就使用比较笨的方法，那就改用 range 方式？

因为 id 是递增的，也很好修改 sql
```sql
select * from mytable where id > offset and id < offset + limit and index_col = xxx
```

explain 显示 type = range, 结果速度非常理想，返回结果快了几十倍。