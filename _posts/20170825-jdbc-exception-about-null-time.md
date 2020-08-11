---
title: Logstash使用jdbc_input同步Mysql数据时遇到的空时间SQLException问题
date: 2017-08-25 13:46:01
tag: 
---

今天在使用Logstash的jdbc_input插件同步Mysql数据时，本来应该能搜索出10条数据，结果在Elasticsearch中只看到了4条，终端中只给出了如下信息
```bash
[2017-08-25T13:31:04,084][INFO ][logstash.pipeline ]Pipeline terminated <“pipeline.id”=>”main”>
```
看不出所以然，到 logstash 的日志 logs 目录下看最新的日志，发现了一些信息。
```bash
[2017-08-25T13:28:02,076][WARN ][logstash.inputs.jdbc ] Exception when executing JDBC query {“exception=>#<Sequel::DatabaseError: Java::JavaSql::SQLException: Value ‘0000-00-00 00:00:00’ can not be represented as java.sql.Timestamp>}
```
原来是JDBC无法处理Mysql中默认的空时间，这个可以通过JDBC连接串中指定配置来将空时间转换为Null，如下：
```yaml
input {
  jdbc {
    jdbc_driver_library => “”
    jdbc_driver_class => “com.mysql.jdbc.Driver”
    jdbc_connection_string => “jdbc:mysql://localhost:3306/dcod?zeroDateTimeBehaviro=convertToNull”
    jdbc_user => “root”
    jdbc_password => “”
    statement => “select * from table"
  }
}
```
重新跑一下 Logstash，OK问题解决。
> 本文的运行环境为 ELK 6.0 beta



参考资料：
1、[JDBC Url参数说明](http://elf8848.iteye.com/blog/1684414)












