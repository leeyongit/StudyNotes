Elasticsearch与多张mysql表同步,logstash conf配置
---
之前已经涉及到利用logstash.conf配置文件使Elasticsearch与mysql一张表进行同步，但实际上在生产环境中肯定不可能只与一张表进行同步，所以必须对conf文件进行修改，这样才能适应多张表同步需求。

```ini
input {
    stdin {
    }
    jdbc {
      jdbc_connection_string => "jdbc:mysql://localhost/数据库名"
      jdbc_user => "root"
      jdbc_password => "password"
      jdbc_driver_library => "mysql-connector-java-5.1.45-bin.jar所在位置"
      jdbc_driver_class => "com.mysql.jdbc.Driver"
      codec => plain {charset => "UTF-8"}
      record_last_run => true
      jdbc_paging_enabled => "true"
      jdbc_page_size => "1000"
      statement => "sql statement"
      schedule => "* * * * *" # 执行 sql 时机，类似 crontab 的调度
      type => "数据库表名1"
      tags => "数据库表名1"
    }
    jdbc {
      jdbc_connection_string => "jdbc:mysql://localhost/数据库名"
      jdbc_user => "root"
      jdbc_password => "password"
      jdbc_driver_library => "mysql-connector-java-5.1.45-bin.jar所在位置"
      jdbc_driver_class => "com.mysql.jdbc.Driver"
      codec => plain {charset => "UTF-8"}
      record_last_run => true
      jdbc_paging_enabled => "true"
      jdbc_page_size => "1000"
      statement => "sql statement"
      schedule => "* * * * *"
      type => "数据库表名2"
      tags => "数据库表名2"
    }
}

filter {
    json {
        source => "message"
        remove_field => ["message"]
    }
}

output {
    if [type] == "数据库表名1"{
        elasticsearch {
            hosts => ["els的host地址"]
            index => "数据库表名1对应的els的index"
            document_id => "%{唯一id}"
        }
    }
    if [type] == "数据库表名2"{
        elasticsearch {
            hosts => ["els的host地址"]
            index => "数据库表名2对应的els的index"
            document_id => "%{唯一id}"
        }
    }
    stdout {
        codec => json_lines
    }
}
```

#### logstash-input-jdbc 读取数据库，当数据库中有新的记录时，进行实时读取

Logstash配置：

```ini
input {
  jdbc {
    jdbc_driver_library => "drivers/mysql-connector-java-5.1.45.jar"
    jdbc_driver_class => "com.mysql.jdbc.Driver"
    jdbc_connection_string => "jdbc:mysql://localhost:3306/mybatis"
    jdbc_user => "root"
    jdbc_password => "root123.."
    parameters => { "gender" => "Male" }
    schedule => "* * * * *"
    statement => "SELECT id,last_name,email,gender from employee where id > :sql_last_value and gender = :gender"
    use_column_value => true
    tracking_column => "id"
  }
}

output {
    stdout { codec => rubydebug }
}
```

1. 重点在于 **use_column_value** 和 **tracking_column** 这两个参数，当use_column_value为true时，可以用 **:sql_last_value** 这个变量来获取tracking_column对应的字段的最新值，默认即第一次启动时为 **0** 。我的示例中tracking_column对应id，即Logstash都会记录每次查询结果id的最大值，供下一次查询使用。

2. Logstash将tracking_column的最新值记录到 **last_run_metadata_path** 参数下的 **.logstash_jdbc_last_run** 文件，默认是**/home/${user}/.logstash_jdbc_last_run**，所以重启后也不会从最初加载，还是从上次记录的最新值开始查。当然，也可以设置 **clean_run** 参数为true，重启后删除上次的运行状态，就可以从最初的数据开始读取了。
