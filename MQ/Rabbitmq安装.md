Rabbitmq 笔记
-----------
提供可靠性消息投递模式（confirm）、返回模式（return）

rabbitmq安装
```sh
yum install rabbitmq-server
chkconfig rabbitmq-server on
/usr/lib/rabbitmq/bin/rabbitmq-plugins list
```
启动web插件 rabbitmq_management
```sh
/usr/lib/rabbitmq/bin/rabbitmq-plugins enable rabbitmq_management
```
启动
```sh
/usr/lib/rabbitmq/bin/rabbitmq-server -detached
```
查看服务状态
```sh
./rabbitmqctl status
```
管理页面 http://192.168.191.11:15672 guest  guest



