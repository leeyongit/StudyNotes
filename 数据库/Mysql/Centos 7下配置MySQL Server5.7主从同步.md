# Centos 7下配置MySQL Server5.7主从同步

## Step 1 — Master数据库服务器配置

```sh
[root@server155 ~]# nano /etc/my.cnf

[mysqld]
log-bin=mysql-bin
server-id=155
binlog-ignore-db=information_schema
#binlog-ignore-db=sys
binlog-ignore-db=performance_schema
binlog-ignore-db=mysql
binlog-do-db=daguanrendb

datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock

symbolic-links=0

log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```

这里的server-id用于标识唯一的数据库，这里设置为155，在设置从库的时候就需要设置为其他值。

binlog-ignore-db：表示同步的时候ignore的数据库

binlog-do-db：指定需要同步的数据库

## Step 2 — Slave数据库服务器配置

```sh
[root@server156 ~]# nano /etc/my.cnf

[mysqld]
log-bin=mysql-bin
server-id=156
binlog-ignore-db=information_schema
#binlog-ignore-db=sys
binlog-ignore-db=performance_schema
binlog-ignore-db=mysql
replicate-do-db=daguanrendb
replicate-ignore-db=mysql
log-slave-updates
slave-skip-errors=all
slave-net-timeout=60
```

## Step 3 — 重启主从数据库

```sh
[root@server155 ~]# service mysqld restart
[root@server156 ~]# service mysqld restart
```

## Step 4 — 进入主数据库，赋予从库权限

```sh
[root@server155 ~]# mysql -u root -p
```

赋予从库权限帐号，允许用户在主库上读取日志，赋予10.0.1.156也就是Slave机器有File权限，只赋予Slave机器有File权限还不行，还要给它REPLICATION SLAVE的权限才可以。

```mysql
mysql> GRANT FILE ON *.* TO 'root'@'10.0.1.156' IDENTIFIED BY 'Daguanren.cc@2017';

mysql> GRANT REPLICATION SLAVE ON *.* TO 'root'@'10.0.1.156' IDENTIFIED BY 'Daguanren.cc@2017';

mysql> flush privileges;
```

## Step 5 — 显示主库信息

```sql
mysql>  show master status;
```

![image-20210330235044302](../assets/image-20210330235044302.png)

这里的 File 、Position 是在配置Salve的时候要使用到的，Binlog_Do_DB表示要同步的数据库，Binlog_Ignore_DB 表示Ignore的数据库，这些都是在配置的时候进行指定的。

## Step 6 — 进入从数据库，配置master

```sql
[root@server156 ~]# mysql -u root -p
mysql> stop slave;
mysql> change master to master_host='10.0.1.155',master_user='root',master_password='Daguanren.cc@2017',master_log_file='mysql-bin.000003', master_log_pos=725;
mysql> start slave;
```

在这里指定Master的信息，master_log_file是在配置Master的时候的File选项， master_log_pos是在配置Master的Position 选项，这里要进行对应。

```sql
mysql> show slave status;
```

## Step 7 — 测试

创建数据库：

```sql
mysql> CREATE DATABASE IF NOT EXISTS iso default charset utf8 COLLATE utf8_general_ci;
```

在主服务器中：

```sql
mysql> use daguanrendb;
```

创建表

```sql
mysql> CREATE TABLE test3
 (
id int
 );
```

插入数据
```sql
mysql> INSERT INTO test3 VALUES ('11');
```

查询
```sql
mysql> select * from test3;
+------+
| id   |
+------+
|   11 |
+------+
1 row in set (0.00 sec)
```

在从服务器中：
```sql
mysql> use daguanrendb;
mysql> show tables;
+---------------------+
| Tables_in_daguanrendb |
+---------------------+
| test3               |
+---------------------+
1 row in set (0.00 sec)
mysql> select * from test3;
+------+
| id   |
+------+
|   11 |
+------+
1 row in set (0.00 sec)
```