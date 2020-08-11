---
title: 利用Logstash插件进行Elasticsearch与Mysql的数据
date: 2017-08-26 17:07:01
tag: 
---

Logstash与Elasticsearch的安装就不多说了，我之前有两篇文章写的比较详细了[ElasticSearch + Logstash + Kibana 搭建笔记](http://www.cnblogs.com/cocowool/p/7297749.html)和[Filebeat+Logstash+ElasticSearch+Kibana搭建Apache访问日志解析平台](http://www.cnblogs.com/cocowool/p/7338426.html)。
Mysql Connector没有包含在ELK的包中，需要自己[下载](https://dev.mysql.com/downloads/connector/j/)。

### 配置文件
最主要的配置文件是 Logstash 的配置，我们命名为 mysql.conf 样例如下
```yaml
input {
    stdin {
    
    }
    jdbc {
        # 数据库地址  端口  数据库名
        jdbc_connection_string => "jdbc:mysql://localhost:3306/shen"
        # 数据库用户名      
        jdbc_user => "root"
        # 数据库密码
        jdbc_password => "rootroot"
        # mysql java驱动地址 
        jdbc_driver_library => "/usr/share/logstash/mysql-connector-java-5.1.43-bin.jar"
        # 驱动类的名称
        jdbc_driver_class => "com.mysql.jdbc.Driver"
        
        jdbc_paging_enabled => "true"
        
        jdbc_page_size => "50000"
        statement => "SELECT * FROM TABLE"
        # sql 语句文件，对于复杂的查询，可以放在文件中。
        # statement_filepath => "filename.sql"
        # 设置监听间隔，语法与Linux系统Cron相同
        schedule => "* * * * *"
    }
}
output {
     stdout {
        codec => json_lines
    }
　　　elasticsearch {
        hosts  => "localhost:9200"
        index => "contacts"
　　　　　document_type => "contact"
        document_id => "%{id}"
    }
}
```

### 进行数据同步
进行数据同步只需要将 Logstash 启动，并且通过```-f```参数指定我们创建的 mysql.conf 配置文件即可，可以通过终端中输出的信息查看同步是否成功。
```bash
./bin/logstash -f mysql.conf
```
> 本例是对一个数据库表进行同步，如果需要同步多个表的数据，可以创建多个配置文件，也可以在一个配置文件中指定多个 jdbc input。配置中的所有项目都必须重新复制一遍。


### 增量更新
这个例子中的SQL执行的全量更新，如果需要进行增量更新，就需要对SQL进行一些修改。
```yaml
input {
    stdin {
    
    }
    jdbc {
        # 数据库地址  端口  数据库名
        jdbc_connection_string => "jdbc:mysql://localhost:3306/shen"
        # 数据库用户名      
        jdbc_user => "root"
        # 数据库密码
        jdbc_password => "rootroot"
        # mysql java驱动地址 
        jdbc_driver_library => "/usr/share/logstash/mysql-connector-java-5.1.43-bin.jar"
        # 驱动类的名称
        jdbc_driver_class => "com.mysql.jdbc.Driver"
        
        jdbc_paging_enabled => "true"
        jdbc_page_size => "50000"

        #是否记录上次运行的结果
        record_last_run => true
        #记录上次运行结果的文件位置
        last_run_metadata_path => ""
        #是否使用数据库某一列的值，
        use_column_value => true
        tracking_column => "id"
        #numeric或者timestamp
        tracking_column_type => "numeric"
        
        #如果为true则会清除 last_run_metadata_path 的记录，即重新开始同步数据
        clean_run => false

        #sql_last_value根据tracking类型，默认为0或者1970-1-1
        statement => "SELECT * FROM TABLE WHERE id > :last_sql_value"
        # sql 语句文件，对于复杂的查询，可以放在文件中。
        # statement_filepath => "filename.sql"
        # 设置监听间隔，语法与Linux系统Cron相同
        schedule => "* * * * *"
    }
}
output {
     stdout {
        codec => json_lines
    }
　　　elasticsearch {
        hosts  => "localhost:9200"
        index => "contacts"
　　　　　document_type => "contact"
        document_id => "%{id}"
    }
}
```
> 增量更新会忽略对历史数据的更新，如果业务中历史数据经常发生变化，则可以通过全量更新的方法。


### 重要参数说明

> 本文配置在 ELK 6.0 beta 环境下测试通过。



参考资料：
1、[Mysql Connector](https://dev.mysql.com/downloads/connector/j/)
2、[ElasticSearch5+logstash的logstash-input-jdbc实现mysql数据同步](http://www.cnblogs.com/phpshen/p/6098333.html)
3、[logstash-input-jdbc实现mysql 与elasticsearch实时同步深入详解](http://blog.csdn.net/laoyang360/article/details/51747266)
4、[logstash input jdbc连接数据库](http://www.cnblogs.com/licongyu/p/5383334.html)
5、[JDBC Plugin](https://www.elastic.co/guide/en/logstash/current/plugins-inputs-jdbc.html#plugins-inputs-jdbc-jdbc_driver_library)