# 使用logstash从 mysql 全量同步数据到elasticsearch

#### 1. 安装elasticsearch

1.1、下载并安装ES的yum公钥

```sh
rpm --import https://packages.elastic.co/GPG-KEY-elasticsearch
```

1.2、配置ES的yum源

1.2、配置ES的yum源

```sh
vim /etc/yum.repos.d/elasticsearch.repo
```

输入下面的内容：

输入下面的内容：

```ini
[elasticsearch-6.x]
name=Elasticsearch repository for 6.x packages
baseurl=https://artifacts.elastic.co/packages/6.x/yum
gpgcheck=1
gpgkey=https://artifacts.elastic.co/GPG-KEY-elasticsearch
enabled=1
autorefresh=1
type=rpm-md
```

1.3.1、更新yum的缓存

```sh
yum makecache
```

1.3.2、安装ES

```shell
yum install elasticsearch
```

#### 2. 安装logstash

2.1安装logstash见官网：

https://www.elastic.co/guide/en/logstash/current/installing-logstash.html
确认logstash是否安装好
切换到logstash/bin目录如：
```sh
cd logstash-6.2.4（使用centos yum安装logstash目录在/usr/share/logstash/bin）
bin/logstash -e 'input { stdin { } } output { stdout {} }'
```
查看输出


2.2添加用户：

切换到elasticearch安装目录执行一下命令

```sh
bin/x-pack/users useradd my_admin -p my_password -r superuser
```

#### 3.创建index此处略

#### 4.下载mysql驱动解压到某个目录这个目录需要配置到步骤4

#### 5.配置mysql.conf并放到目录/usr/share/logstash/bin/config-mysql/mysql.conf

mysql.conf内容

```ini
input {
    stdin{
    }
    jdbc {
      # 数据库
      jdbc_connection_string => "jdbc:mysql://localhost:3306/mydatabase"
      jdbc_user => "root"
      jdbc_password => "数据库密码"
      # mysql驱动解压的位置
      jdbc_driver_library => "/usr/share/logstash/data/mysql-connector-java-5.1.46/mysql-connector-java-5.1.46-bin.jar"
       # mysql驱动类
      jdbc_driver_class => "com.mysql.jdbc.Driver"
      jdbc_paging_enabled => "true"
      jdbc_page_size => "50000"
      #可以使用sql文件的方式
      #statement_filepath => "config-mysql/tag.sql"
      #要同步的表
      statement => "SELECT * from tbl_tag"
      schedule => "* * * * *"
      #索引type
      type => "tag"
      #索引设置时区
      jdbc_default_timezone => "Asia/Shanghai"
    }
}


output {
    elasticsearch {
        hosts => "localhost:9200"
				#索引名称
        index => "myindex"
        document_id => "%{id}"
        user => elastic用户名
        password => "elastic登录密码"
				#输出的索引type此处表示上面的tag
       "document_type" => "%{type}"
    }
    stdout {
        codec => json_lines
    }
}
```

#### 6.切换使用命令查看是否成功 需要root用户

```sh
./logstash -f config-mysql --path.data=/root/
```

