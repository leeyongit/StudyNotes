Mysql 问题集锦
===
## 1. MySQL的sql_mode解析与设置
因为在MySQL中使用group by 是总是出现1055的错误，这就导致了必须去查看是什么原因了，查询了相关的资料，现在将笔记记录下来，以便后面可以参考使用：
**sql_mode:简而言之就是：它定义了你MySQL应该支持的sql语法，对数据的校验等等**
select @@sql_mode:使用该命令我们可以查看我们当前数据库的sql_mode

```mysql
mysql> select @@sql_mode;
+------------------------------------------------------------------------------------------+
| @@sql_mode                                                                                                                                |
+------------------------------------------------------------------------------------------+
| ONLY_FULL_GROUP_BY,STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION |
+------------------------------------------------------------------------------------------+
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
```ini
sql_mode= STRICT_TRANS_TABLES,NO_ZERO_IN_DATE,NO_ZERO_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER,NO_ENGINE_SUBSTITUTION
```

**NO_ZERO_IN_DATE：**

 在严格模式，不接受月或日部分为0的日期。如果使用IGNORE选项，我们为类似的日期插入'0000-00-00'。在非严格模式，可以接受该日期，但会生成警告。

**NO_ZERO_IN_DATE：**

 在严格模式，不接受月或日部分为0的日期。如果使用IGNORE选项，我们为类似的日期插入'0000-00-00'。在非严格模式，可以接受该日期，但会生成警告。

## 2. Mysql 允许远程连接

```mysql
GRANT ALL PRIVILEGES ON *.* TO'root'@'%' IDENTIFIED BY 'root' WITH GRANT OPTION;
flush privileges;
```

## 3. MySql事务- Lock wait timeout exceeded; try restarting transaction问题解决
背景：现网报环境很慢，提交没有响应。
日志：Lock wait timeout exceeded; try restarting transaction
产生原因：
当事务A对记录1进行更新或者删除操作的请求未commit时，另一个事务B也对记录1进行更新或者删除操作，此时事务B回等待前一个事务A提交事务，释放行锁，如果这个时间超过mysql设置的超时时间，则会产生“LOCK WAIT”事务。
解决方法:
1.查看数据库当前的进程

```sql
mysql> show  processlist;
```
看一下有无正在执行的慢SQL记录线程。
2.查看当前的事务
```mysql
#当前运行的所有事务
mysql> SELECT * FROM information_schema.INNODB_TRX;

#当前出现的锁
mysql> SELECT * FROM information_schema.INNODB_LOCKs;

#锁等待的对应关系
mysql> SELECT * FROM information_schema.INNODB_LOCK_waits;
```
解释：看事务表INNODB_TRX，里面是否有正在锁定的事务线程，看看ID是否在show processlist里面的sleep线程中，如果是，就证明这个sleep的线程事务一直没有commit或者rollback而是卡住了，我们需要手动kill掉。

搜索的结果是在事务表发现了很多任务，这时候最好都kill掉

3.批量删除事务表中的事务
我这里用的方法是：通过information_schema.

```mysql
mysql>  select concat('KILL ',id,';') from information_schema.processlist where user='root';
+------------------------+
| concat('KILL ',id,';') |
+------------------------+
| KILL 10508;            |
| KILL 10521;            |
| KILL 10297;            |
+------------------------+
18 rows in set (0.00 sec)
```

## 4. 使用mysqlbinlog查看MySQL二进制文件内容

一般都可以通过以下命令将二进制格式的binlog转换为方便查看的文本输出。下面会详细讲解各个参数的作用。

```bash
mysqlbinlog  --no-defaults --base64-output=DECODE-ROWS -vv  binlog.000130
```

MySQL的二进制日志由包含“ 事件 ”（event）的文件组成，这些 事件描述了对数据库内容的修改。服务器以二进制格式写入这些文件。要以文本格式显示其内容，请使用 mysqlbinlog实用程序。你还可以使用 mysqlbinlog显示复制设置中从属服务器写入的中继日志文件的内容，因为中继日志的格式与二进制日志相同

> --no-defaults  不会去读取一些默认的配置文件，因为读取这些文件的时候使用mysqlbinlog会报错。加上这个选项后可以避免读取配置文件而报错
>
> 加上--base64-output=DECODE-ROWS 选项后，行事件的binlog语句（那些看不懂的字符串）就不再显示。
> 选项 --base64-output=DECODE-ROWS -v 组合成为一个只看注释的伪SQL便捷组合。
>
> 使用两次 -v -v 或者 -vv 或者 --verbose --verbose可以额外显示字段的类型以及字段的一些元数据信息。更加详细了