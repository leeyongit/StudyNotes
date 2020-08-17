# Mysql 导入导出


## 一、导出数据库

#### 1. 导出数据和表结构：
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
```sh
mysqldump -uroot -p dbname > dbname.sql
```

#### 2. 导出一个表
mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
```sh
mysqldump -u dbuser -p dbname users> dbname_users.sql
```

#### 3. 只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
```sh
mysqldump -uroot -p -d dbname > dbname.sql
```
```sh
mysqldump -u dbuser -p -d --add-drop-table dbname >d:/dbname_db.sql
```
> -d 没有数据
> --add-drop-table 在每个create语句之前增加一个drop table

#### 4. mysqldump命令之single-transaction

mysqldump 导出报错!

```ini
mysqldump: Got error: 1045: Access denied for user 'jupiter'@'10.141.136.49' (using password: YES) when using LOCK TABLES
```

在mysqldump中指定single-transaction时，会使用可重复读(REPEATABLE READ)事务隔离级别来保证整个dump过程中数据一致性，该选项仅对InnoDB表

有用，且不能与ALTER TABLE/CREATE TABLE/DROP TABLE/RENAME TABLE/TRUNCATE TABLE等DDL操作并行。

```mysql
# mysqldump的操作步骤如下：
FLUSH TABLES;
FLUSH TABLES WITH READ LOCK;
SET SESSION TRANSACTION ISOLATION LEVEL REPEATABLE READ;
START TRANSACTION WITH CONSISTENT SNAPSHOT;
SHOW MASTER STATUS;
UNLOCK TABLES;

SHOW TABLES LIKE 'xxx'
SET OPTION SQL_QUOTE_SHOW_CREATE=1
SHWO CREATE TABLE 'xxx'
SHOW FIELDS FROM 'xxx'
SHOW TABLE STATUS LIKE 'xxx'
SELECT /*!40001 SQL_NO_CACHE */ * FROM  xxx

QUIT
```
通过FLUSH TABLES WITH READ LOCK来锁定所有表，然后开启事务，由于外部事务不能对表数据进行修改，因此SHOW MASTER STATUS的数据不会发生变化，而由于事务隔离级别为REPEATABLE READ，因此在整个mysqldump过程中，获取到的数据为开始事务时的数据，因此可以保证mysqldump出来的数据一致性，并可以结合SHOW MASTER STATUS出来的数据进行恢复。
```bash
mysqldump --single-transaction -h host -u user -p database > database.sql
```

## 二、导入数据库

#### 方法一：

（1）选择数据库
```sh
mysql>use abc;
```
（2）设置数据库编码
```sh
mysql>set names utf8;
```
（3）导入数据（注意sql文件的路径）
```sh
mysql>source /home/abc/abc.sql;
```

#### 方法二：
mysql -u用户名 -p密码 数据库名 < 数据库名.sql
```sh
mysql -uabc_f -p abc < abc.sql
```

