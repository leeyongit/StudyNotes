# supervisord管理
---
## 安装 & 启动
```sh
yum install -y epel-release supervisor
supervisord -c /etc/supervisord.conf 
```
## 配置例子
```sh
[program:nginx]
process_name=%(program_name)s_%(process_num)02d
command=/sbin/nginx                                   ; 程序路径
autostart=true
autorestart=true                                      ; 自动重启
priority=999                                          ; 优先级
startsecs=1                                           ; 重启前等待时间
startretries=100                                      ; 最大重启次数
user=www
numprocs=8
redirect_stderr=true                                  ; 如果为true，则stderr的日志会被写入stdout日志文件中默认为false，非必须设置
stdout_logfile=/path/app.log                          ; 输出日志
stderr_logfile=/path/app.log                          ; 错误日志
```
## 重载配置
```sh
supervisorctl reread && supervisorctl update
```
## 终端交互
```
supervisorctl
```

## logstash
--path.data=./data data目录必须有写权限
```sh
[program:logstash-indexer]
command = /home/elk/logstash-6.2.3/bin/logstash -f /home/elk/logstash-6.2.3/config/kafka-logstash-es.conf --path.data=/home/elk/logstash-6.2.3/data
autostart = true
autorestart = true
startsecs = 5
startretries = 3
user = elsearch 
redirect_stderr = true
stdout_logfile=/home/elk/log/logstash-indexer-std.log
stderr_logfile=/home/elk/log/logstash-indexer-err.log
```

```sh
[program:elasticsearch-node1]
command = /home/elk/elasticsearch-6.2.3/bin/elasticsearch
autostart = true
autorestart = true
startsecs = 5
startretries = 3
user = elsearch
redirect_stderr = true
stdout_logfile=/home/elk/log/elasticsearch-node1-std.log
stderr_logfile=/home/elk/log/elasticsearch-node1-err.log
```
```sh
[program:kibana]
command = /home/elk/kibana-6.2.3/bin/kibana
autostart = true
autorestart = true
startsecs = 5
startretries = 3
user = root 
redirect_stderr = true
stdout_logfile=/home/elk/log/kibana-std.log
stderr_logfile=/home/elk/log/kibana-err.log
``
