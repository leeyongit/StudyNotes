Mysql 问题解决
---

#### 1. MySQL的sql_mode解析与设置
因为在MySQL中使用group by 是总是出现1055的错误，这就导致了必须去查看是什么原因了，查询了相关的资料，现在将笔记记录下来，以便后面可以参考使用：
sql_mode:简而言之就是：它定义了你MySQL应该支持的sql语法，对数据的校验等等
select @@sql_mode:使用该命令我们可以查看我们当前数据库的sql_mode
```sql
mysql> select @@sql_mode;
+-------------------------------------------------------------------------------------------------------------------------------------------+
| @@sql_mode                                                                                                                                |
+-------------------------------------------------------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+-------------------------------------------------------------------------------------------------------------------------------------------+
1 row in set (0.00 sec)
```
下面我们来看看sql_mode的各个值的含义：

**ONLY_FULL_GROUP_BY：**

对于GROUP BY聚合操作，如果在SELECT中的列，没有在GROUP BY中出现，那么将认为这个SQL是不合法的，因为列不在GROUP BY从句中

因为有only_full_group_by，所以我们要在MySQL中正确的使用group by语句的话，只能是select column1 from tb1 group by column1(即只能展示group by的字段，其他均都要报1055的错)

所以我们要使用能正确的使用group by 的话就必须删除掉only_full_group_by

set sql_mode=(select replace(@@sql_mode,'ONLY_FULL_GROUP_BY','')); 可以使用该语句来将空格替换掉only_full_group_by，这样我们就可以使用
```sql
SET sql_mode=(SELECT REPLACE(@@sql_mode,'ONLY_FULL_GROUP_BY',''));
```
但是这种方法只是做了暂时的修改，我们可以更改配置文件my.ini
```sql
sql_mode= STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

**NO_ZERO_IN_DATE：**

 在严格模式，不接受月或日部分为0的日期。如果使用IGNORE选项，我们为类似的日期插入'0000-00-00'。在非严格模式，可以接受该日期，但会生成警告。

**NO_ZERO_IN_DATE：**

 在严格模式，不接受月或日部分为0的日期。如果使用IGNORE选项，我们为类似的日期插入'0000-00-00'。在非严格模式，可以接受该日期，但会生成警告。

#### 2. Mysql 允许远程连接

```mysql
GRANT ALL PRIVILEGES ON *.* TO'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
flush privileges;
```
