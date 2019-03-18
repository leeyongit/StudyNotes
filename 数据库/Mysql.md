# Mysql

### Mysql 允许远程连接
```sql
GRANT ALL PRIVILEGES ON *.* TO'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
flush privileges;
```

### SQL组成：
DDL：数据库模式定义语言，关键字：create
DML：数据操纵语言，关键字：Insert、delete、update
DCL：数据库控制语言 ，关键字：grant、remove
DQL：数据库查询语言，关键字：select

### MySQL的sql_mode解析与设置

```sql
set global sql_mode='STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION';
```

## linux下 mysql导入导出sql文件

### 一、导出数据库用mysqldump命令（注意mysql的安装路径，即此命令的路径）：
1. 导出数据和表结构：
mysqldump -u用户名 -p密码 数据库名 > 数据库名.sql
```sh
/usr/local/mysql/bin/ mysqldump -uroot -p abc > abc.sql
```
> 敲回车后会提示输入密码

2. 只导出表结构
mysqldump -u用户名 -p密码 -d 数据库名 > 数据库名.sql
```sh
/usr/local/mysql/bin/  mysqldump -uroot -p -d abc > abc.sql
```
> 注：/usr/local/mysql/bin/  --->  mysql的data目录


### 二、导入数据库
1. 首先建空数据库
```sh
mysql>create database abc;
```
2. 导入数据库

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