# 使用logstash+elasticsearch+kibana快速搭建日志平台

日志的分析和监控在系统开发中占非常重要的地位，系统越复杂，日志的分析和监控就越重要，常见的需求有:

    根据关键字查询日志详情
    监控系统的运行状况
    统计分析，比如接口的调用次数、执行时间、成功率等
    异常数据自动触发消息通知
    基于日志的数据挖掘

很多团队在日志方面可能遇到的一些问题有:

    开发人员不能登录线上服务器查看详细日志，经过运维周转费时费力
    日志数据分散在多个系统，难以查找
    日志数据量大，查询速度慢
    一个调用会涉及多个系统，难以在这些系统的日志中快速定位数据
    数据不够实时

在系统规模大到一定程度前推荐轻量级下载即用的方案，比如logstash+elasticsearch+kibana(LEK)组合。

对于日志来说，最常见的需求就是收集、查询、显示，正对应logstash、elasticsearch、kibana的功能。

**日志/消息整体流向Flume => kafka => logstash => elasticsearch => kibana**



## 安装 Elasticsearch

- [Download Elasticsearch](https://www.elastic.co/downloads/elasticsearch)
- [ELK学习系列文章第二章：elasticsearch常见错误与配置简介](https://blog.csdn.net/qq_21387171/article/details/53577115)

### 下载解压

```sh
cd /home
sudo mkdir elk
cd /elk
sudo wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-6.2.3.zip
sudo unzip elasticsearch-6.2.3.zip
```

### 增加es用户组、用户

更改elasticsearch文件夹及内部文件的所属用户及组为elsearch:elsearch
```sh
groupadd elsearch
useradd elsearch -g elsearch -p elasticsearch
chown -R elsearch:elsearch elasticsearch-6.2.3
```

首先启动ES服务，切换到elasticsearch目录，运行bin下的elasticsearch
```sh
cd /home/elk/elasticsearch-6.2.3
nohup su elsearch bin/elasticsearch 1>/dev/null 2>&1 &
```

### 修改ip
```sh
vim config/elasticsearch.yml
network.host 39.107.158.137
```

### 修改max_map_count值
```sh
sudo sysctl -w vm.max_map_count=262144
```

访问默认的9200端口
```sh
curl -X GET http://localhost:9200

```

## Logstash

### 下载安装

- [下载 Logstash](https://www.elastic.co/downloads/logstash)

```sh
cd /search
sudo mkdir logstash
cd logstash
sudo wget https://artifacts.elastic.co/downloads/logstash/logstash-6.2.3.zip
cd logstash-6.2.3
```

先测试是否安装成功
```sh
> bin/logstash -e 'input{stdin{}}output{stdout{codec=>rubydebug}}'
Settings: Default pipeline workers: 4
Pipeline main started
hello world! # 输入测试字符串
{
    "@version" => "1",
    "@timestamp" => 2018-04-13T16:29:37.991Z,
    "message" => "hello world!",
    "host" => "otc"
}
```

### 配置

新建kafka-logstash-es.conf
```ini
cd config
vim kafka-logstash-es.conf

input {
    kafka {
        type => "fshd"
        bootstrap_servers => ["114.118.13.66:9092,114.118.13.66:9093,114.118.13.66:9094"]
        client_id => "es_fshd"
        group_id => "es_fshd"
        auto_offset_reset => "latest" # 从最新的偏移量开始消费
        consumer_threads => 5
        decorate_events => true # 此属性会将当前topic、offset、group、partition等信息也带到message中
        topics => ["fshd_nginx_access_log"] # 数组类型，可配置多个topic
        tags => ["nginx", "fshd_access"]

    }
}
filter {
    if [type] == "fshd" {
         grok {
            patterns_dir => [ "./patterns" ]
            match => { "message" => "%{NGINXACCESS}" }
            # match => { "message" => "%{COMBINEDAPACHELOG}" }
            remove_field => ["message"]
        }
        date {
            match => [ "time_local" , "YYYY-MM-dd:HH:mm:ss Z" ]
        }
        geoip {
            source => "clientip"
        }
        kv {
            source => "request"
            field_split => "&?"
            value_split => "="
            include_keys => [ "c", "m", "a", "versionCode","model","osmodel","play_url","play_type" ]
        }
        #mutate {
            #convert => ["request_time", "float"]
        #}
        urldecode {
            all_fields => true
        }
    }
}
output {
    if [type] == "fshd" {
        elasticsearch {
            hosts => ["114.118.10.253:9200"]
            index => "fshd_log-%{+YYYY-MM-dd}"
            type => "log"
            timeout => 300
        }
        stdout { codec => rubydebug }
    }
}
```
HTTPD24_ERRORLOG \[%{HTTPDERROR_DATE:timestamp}\] \[%{WORD:module}:%{LOGLEVEL:loglevel}\] \[pid %{POSINT:pid}:tid %{NUMBER:tid}\]( \(%{POSINT:proxy_errorcode}\)%{DATA:proxy_errormessage}:)?( \[client %{IPORHOST:client}:%{POSINT:clientport}\])? %{DATA:errorcode}: %{GREEDYDATA:message}
### 启动logstash

服务管理

    设置服务自启动：systemctl enable logstash
    启动服务：systemctl start logstash
    停止服务：systemctl stop logstash
    重启服务：systemctl restart logstash
    查看服务状态：systemctl status logstash

如何执行按指定配置文件执行

```sh
nohup bin/logstash -f config/kafka-logstash-es.conf --path.data=./data 1>/dev/null 2>&1 &
nohup bin/logstash -f config/kafka-logstash-fshd_php_error_log-es.conf --path.data=./fshd_php_error_data 1>/dev/null 2>&1 &
```

参数:
* -w  指定线程,默认是 cpu 核数
* -f  指定配置文件
* -t  测试配置文件是否正常
* -b  执行 filter 模块之前最大能积累的日志,数值越大性能越好,同时越占内存

### Logstash收集nginx日志之使用grok过滤插件解析日志

grok作为一个logstash的过滤插件，支持根据模式解析文本日志行，拆成字段。

nginx日志的配置：

```sh
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for" $request_time "$scheme"';
```

```sh
$ cd logstash
$ mkdir patterns # 可以在grok中通过 patterns_dir 指定，默认是这个位置
$ vim patterns/nginx
```

```sh
NGINXACCESS %{COMBINEDAPACHELOG} %{QS:http_x_forwarded_for} %{NUMBER:request_time:float} %{DATA:scheme}
```
```sh
COMMONAPACHELOG %{IPORHOST:clientip} %{USER:ident} %{USER:auth} \[%{HTTPDATE:timestamp}\] "(?:%{WORD:verb} %{NOTSPACE:req
uest}(?: HTTP/%{NUMBER:httpversion})?|%{DATA:rawrequest})" %{NUMBER:response} (?:%{NUMBER:bytes}|-)
```
grok 的 match：
```
%{IPORHOST:clientip} - (%{USERNAME:user}|-) \[%{HTTPDATE:log_timestamp}\] "%{WORD:http_method} %{NOTSPACE:request_uri} (?:HTTP\/\d\.\d)" %{NUMBER:http_status} %{NUMBER:body_bytes_send} "%{DATA:http_refer}" "%{DATA:user_agent}" "%{DATA:x_forword_for}" %{NUMBER:action_length_time} "%{DATA:scheme}"
```
### 安装Ruby(换yum源安装)

```sh
yum install centos-release-scl-rh　//会在/etc/yum.repos.d/目录下多出一个CentOS-SCLo-scl-rh.repo源
yum install rh-ruby23  -y　　　　   //直接yum安装即可　　
scl  enable  rh-ruby23 bash　　　　 //必要一步
ruby -v　　　　                     //查看安装版本
```


## Kibana

### 下载kibana
logstash的最新版已经内置kibana，你也可以单独部署kibana。kibana3是纯粹JavaScript+html的客户端，所以可以部署到任意http服务器上。
```sh
cd /elk
sudo wget https://artifacts.elastic.co/downloads/kibana/kibana-6.2.3-linux-x86_64.tar.gz
tar -zxvf kibana-6.2.3-linux-x86_64.tar.gz
cd kibana-6.2.3-linux-x86_64
```

### 修改配置文件
```sh
vim config/kibana.yml

server.name: kibana
server.host: "0"
elasticsearch.url: http://elasticsearch:9200 # 修改为ip
xpack.monitoring.ui.container.elasticsearch.enabled: false
```
### 启动kibana
```sh
nohup bin/kibana 1>/dev/null 2>&1 &
```
访问
```
http://39.107.158.137:5601
```
