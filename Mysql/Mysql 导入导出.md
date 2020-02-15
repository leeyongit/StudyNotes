Mysql 导入导出
---

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