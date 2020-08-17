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
      schedule => "* * * * *"
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