Mysql5.6主从复制
---------------
主服务IP：192.168.1.117 从服务IP：192.168.1.118

1. 修改主服务my.cnf，重启mysql服务
```shell
   [mysqld]
   innodb_buffer_pool_size = 512M
   log_bin = mysqlmaster-bin.log
   server_id = 117
```

2. 修改从服务my.cnf，重启mysql服务
```shell
  [mysqld]
  innodb_buffer_pool_size = 512M
  log_bin = mysqlslave-bin.log
  server_id = 118  #大于主服务server_id
```
配置从实例的 server-id 和要同步的数据库。
```shell
[mysqld]
server-id               =  123456789              //服务 ID，主从实例 server-id 需不同。
log_bin                 =  /var/log/mysql/mysql-bin.log
expire_logs_days        =  10                     // 主要用来控制binlog日志文件保留时间，超过保留时间的binlog日志会被自动删除。单位（天）
max_binlog_size         =  500M                   // bin log日志每达到设定大小后，会使用新的bin log日志
replicate-do-db         =  exampledb              // 需要同步的数据库
replicate-ignore-db     =  mysql                  // 不需要同步的数据库
replicate-ignore-db     =  information_schema     // 不需要同步的数据库
replicate-ignore-db     =  performance_schema     // 不需要同步的数据库
```
配置 GTID 同步模式，binlog 格式为 row，以兼容主实例。
```sh
#GTID
gtid_mode=on
enforce_gtid_consistency=on
binlog_format=row                             //设置 binlog 为 row
log-slave-updates=1

[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
innodb_buffer_pool_size = 512M
log_bin = mysqlslave-bin.log
server_id = 1777804597
binlog_format=mixed
expire_logs_days        =  10
max_binlog_size         =  500M
#replicate-do-db         =  null_ifensi
replicate-do-table = null_ifensi.fs_y_room
slave-skip-errors = 1032
gtid_mode=on
enforce_gtid_consistency=on
binlog_format=row
log-slave-updates=1
```
3. 192.168.1.117创建用于主从复制的账户并复制数据

(1)创建账户(192.168.1.118上一样)
```sh
mysql>GRANT REPLICATION SLAVE ON *.* TO 'sync-1'@'192.168.1.118' IDENTIFIED BY '123456';
```
(2)数据库锁表(192.168.1.118上一样)
```sh
mysql>FLUSH TABLES WITH READ LOCK;
```
(3)查看master状态
```sh
mysql> show master status;
+------------------------+----------+--------------+------------------+-------------------+
| File                   | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------------+----------+--------------+------------------+-------------------+
| mysqlmaster-bin.000015 |      120 |              |                  |                   |
+------------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```
   二进制日志文件是mysqlmaster-bin.000015，位置是120

(4)复制数据并解锁
```sh
mysqldump -uroot -proot --all-databases --triggers --routines --events >all.sql
mysql>UNLOCK TABLES;
```

4. 192.168.1.118上设置
(1)导入all.sql
```sh
create DATABASE null_ifensi;
mysql -uroot null_ifensi < all.sql
```
(2)从数据库设置复制的主数据库信息(192.168.1.117上一样)
```sh
mysql> CHANGE MASTER TO MASTER_HOST='192.168.1.117',MASTER_USER='sync-1',MASTER_PASSWORD='123456',MASTER_LOG_FILE='mysqlmaster-bin.000015',MASTER_LOG_POS=120;
mysql> START slave;
mysql> SHOW slave STATUS \G
```
显示Slave_IO_Running: Yes Slave_SQL_Running: Yes则表示成功，如果Slave_IO_Running:connecting，则可能是防火墙的原因


**GTID复制异常的解决**
mysql开启GTID跳过错误的方法
```sh
rds> show global variables like '%gtid%';
slave>
reset master;
set global gtid_purged = 'a1cc176b-5230-11e8-b578-6c92bf60831c:1-668677467,
efcd7239-927f-11e6-8e28-7cd30ab8f7e4:1-112202382,
ffd58b84-927f-11e6-8e29-58605f589dfb:1-451531593';
```
**mysql-5.6.20主从同步错误之Error_code: 1032; handler error HA_ERR_KEY_NOT_FOUND**
解决的办法：
1.最好的办法是升级数据库 保证bug不会重现。
2.利用配置参数 来躲避这个bug    vi /etc/my.cnf
  slave-skip-errors = 1032,xxxx,xxxx ....
3.临时逃避此次错误。
　set global sql_slave_skip_counter=1;   stop slave; start slave;

