# Mysql 笔记

#### SQL组成：

- DDL：数据库模式定义语言，关键字：create
- DML：数据操纵语言，关键字：Insert、delete、update
- DCL：数据库控制语言 ，关键字：grant、remove
- DQL：数据库查询语言，关键字：select

#### MySQL的sql_mode解析与设置

```mysql
set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

#### Mysql 允许远程连接

```mysql
GRANT ALL PRIVILEGES ON *.* TO'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
flush privileges;
```

### 

### 一、导出数据库

1. 导出数据和表结构：
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
```sh
mysqldump -uroot -p dbname > dbname.sql
```

2. 导出一个表
mysqldump -u 用户名 -p 数据库名 表名> 导出的文件名
```sh
mysqldump -u dbuser -p dbname users> dbname_users.sql
```

3. 只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
```sh
mysqldump -uroot -p -d dbname > dbname.sql
```
```sh
mysqldump -u dbuser -p -d --add-drop-table dbname >d:/dbname_db.sql
```
-d 没有数据 
--add-drop-table 在每个create语句之前增加一个drop table

### 二、导入数据库
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

### 问题记录
1. mysqldump 导出报错
mysqldump: Got error: 1045: Access denied for user 'jupiter'@'10.141.136.49' (using password: YES) when using LOCK TABLES
```sh
mysqldump --single-transaction -h host -u user -p database > database.sql
```