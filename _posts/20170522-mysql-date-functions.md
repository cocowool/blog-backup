---
title: Mysql中与时间相关的统计分析
date: 2017-05-22 10:52:01
tag: 
keywords: mysql, mysql 时间函数, mysql 时间统计
description: 最近项目需要统计一段日期范围内，根据每分钟、几分钟、每天分别统计汇总某些事件/指标的发生总次数，平均发生次数，因此总结了Mysql中与时间处理、统计相关的资料。
---

最近项目需要统计一段日期范围内，根据每分钟、几分钟、每天分别统计汇总某些事件/指标的发生总次数，平均发生次数，因此总结了Mysql中与时间处理、统计相关的资料。

### 按分钟统计某一时间段内的数据
```sql
SELECT count(1), date_format(a5_firsttime, "%H:%i") as t from ccb_alerts group by t order by null
```
这个统计汇总方法，可以按照每天、工作日（周一至周五）、小时、分钟、月等维度来汇总统计。Mysql的日期输出格式可以参考下面：

如果想要统计一天之内，按照一定的分钟间隔汇总的数据，用SQL实现，还不知道有什么方便的方式，如果大家有相关的解决方案，可以分享一下。
> 2017-05-31补充一下支持跨天的按照一定时间间隔统计汇总数量的SQL，例子是按照15分钟汇总统计
```sql
select count(1), from_unixtime( round(unix_timestamp(a5_firsttime)/(15*60)) * 15*60) from ccb_alerts where a5_firsttime >= '2017-03-20 00:00:00' group by round(unix_timestamp(a5_firsttime)/(15*60))
```


### 日期格式化函数
Mysql日期、时间转换函数：date_format(date, format)、time_format(time,format)能够把一个日期/时间转换成各种各样的字符串。
```sql
date_fromat(date, format)
```

### TIMSTAMPADD
按照输入的间隔对日期进行计算。
```sql
timestampadd(unit,interval,datetime_expr)

select timestampadd(day, 1, '2008-08-08 08:00:00'); -- 2008-08-09 08:00:00
```

### TIMESTAMPDIFF
Mysql中计算两个日期的时间差函数TIMESTAMPDIFF用法:

#### 语法
TIMESTAMPDIFF(interval,datetime_expr1,datetime_expr2)

#### 说明
返回日期或日期时间表达式datetime_expr1 和datetime_expr2the 之间的整数差。其结果的单位由interval 参数给出。interval 的法定值同TIMESTAMPADD()函数说明中所列出的相同。

参考资料：
1、[Mysql中Timstampdiff的用法](http://blog.csdn.net/xb12369/article/details/8646801)
2、[Mysql的时间、日期函数](http://www.cnblogs.com/ggjucheng/p/3352280.html)
3、[Mysql Date Time Functions](https://dev.mysql.com/doc/refman/5.7/en/date-and-time-functions.html)