Mysql数据库之Binlog日志使用总结(必看篇)
----

binlog二进制日志对于mysql数据库的重要性有多大，在此就不多说了。下面根据本人的日常操作经历，并结合网上参考资料，对binlog日志使用做一梳理：

### 一、binlog日志介绍
1）什么是binlog
binlog日志用于记录所有更新了数据或者已经潜在更新了数据（例如，没有匹配任何行的一个DELETE）的所有语句。语句以“事件”的形式保存，它描述数据更改。

2）binlog作用
因为有了数据更新的binlog，所以可以用于实时备份，与master/slave主从复制结合。

3）和binlog有关参数
log_bin
设置此参数表示启用binlog功能，并指定路径名称

log_bin_index
设置此参数是指定二进制索引文件的路径与名称
binlog_do_db
此参数表示只记录指定数据库的二进制日志
binlog_ignore_db
此参数表示不记录指定的数据库的二进制日志
max_binlog_cache_size
此参数表示binlog使用的内存最大的尺寸
binlog_cache_size
此参数表示binlog使用的内存大小，可以通过状态变量binlog_cache_use和binlog_cache_disk_use来帮助测试。
binlog_cache_use：使用二进制日志缓存的事务数量
binlog_cache_disk_use:使用二进制日志缓存但超过binlog_cache_size值并使用临时文件来保存事务中的语句的事务数量

max_binlog_size
Binlog最大值，最大和默认值是1GB，该设置并不能严格控制Binlog的大小，尤其是Binlog比较靠近最大值而又遇到一个比较大事务时，为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的所有SQL都记录进当前日志，直到事务结束
sync_binlog
这个参数直接影响mysql的性能和完整性
sync_binlog=0
当事务提交后，Mysql仅仅是将binlog_cache中的数据写入Binlog文件，但不执行fsync之类的磁盘 同步指令通知文件系统将缓存刷新到磁盘，而让Filesystem自行决定什么时候来做同步，这个是性能最好的。
sync_binlog=n，在进行n次事务提交以后，Mysql将执行一次fsync之类的磁盘同步指令，同志文件系统将Binlog文件缓存刷新到磁盘。
Mysql中默认的设置是sync_binlog=0，即不作任何强制性的磁盘刷新指令，这时性能是最好的，但风险也是最大的。一旦系统绷Crash，在文件系统缓存中的所有Binlog信息都会丢失

4）binlog的删除
binlog的删除可以手工删除或自动删除：
a）自动删除binlog
通过binlog参数（expire_logs_days ）来实现mysql自动删除binlog
```sh
mysql> show binary logs;
mysql> show variables like 'expire_logs_days';      //该参数表示binlog日志自动删除/过期的天数，默认值为0，表示不自动删除
mysql> set global expire_logs_days=3;        //表示日志保留3天，3天后就自动过期。
```
b）手工删除binlog
```sh
mysql> reset master;        //删除master的binlog，即手动删除所有的binlog日志
mysql> reset slave;          //删除slave的中继日志
mysql> purge master logs before '2012-03-30 17:20:00';         //删除指定日期以前的日志索引中binlog日志文件
mysql> purge master logs to 'binlog.000002';       //删除指定日志文件的日志索引中binlog日志文件

mysql> set sql_log_bin=1/0;       //如果用户有super权限，可以启用或禁用当前会话的binlog记录
mysql> show master logs;          //查看master的binlog日志列表
mysql> show binary logs;           //查看master的binlog日志文件大小
mysql> show master status;     //用于提供master二进制日志文件的状态信息
mysql> show slave hosts;        //显示当前注册的slave的列表。不以--report-host=slave_name选项为开头的slave不会显示在本列表中

mysql> flush logs;     //产生一个新的binlog日志文件
```


#### mysql binlog日志自动清理及手动删除案例说明：

当开启MySQL数据库主从时，会产生大量如mysql-bin.00000* log的文件，这会大量耗费您的硬盘空间。
mysql-bin.000001
mysql-bin.000002
mysql-bin.000003
mysql-bin.000004
mysql-bin.000005
…

删除这些binlog日志有三种解决方法：
1.关闭mysql主从，关闭binlog；
实例操作如下：
[root@huqniupc ~]# vim /etc/my.cnf  //注释掉log-bin和binlog_format
# Replication Master Server (default)
# binary logging is required for replication
# log-bin=mysql-bin
# binary logging format - mixed recommended
# binlog_format=mixed
然后重启数据库

2.开启mysql主从，设置expire_logs_days；
实例操作如下：
[root@huqniupc ~]# vim /etc/my.cnf //修改expire_logs_days,x是自动删除的天数，一般将x设置为短点，如10
expire_logs_days = x //二进制日志自动删除的天数。默认值为0,表示“没有自动删除”
此方法需要重启mysql

当然也可以不重启mysql,开启mysql主从，直接在mysql里设置expire_logs_days
> show binary logs;
> show variables like '%log%';
> set global expire_logs_days = 10;


3.手动清除binlog文件，(比如Mysql> PURGE MASTER LOGS TO ‘MySQL-bin.010′;）
实例操作如下：
[root@huqniupc ~]# /usr/local/mysql/bin/mysql -u root -p
> PURGE MASTER LOGS BEFORE DATE_SUB(CURRENT_DATE, INTERVAL 10 DAY);  //删除10天前的MySQL binlog日志,附录2有关于PURGE MASTER LOGS手动删除用法及示例
> show master logs;

也可以重置master，删除所有binlog文件：
# /usr/local/mysql/bin/mysql -u root -p
> reset master; //附录3有清除binlog时，对从mysql的影响说明

---------------------------------------------------------------
PURGE MASTER LOGS手动删除用法及示例,MASTER和BINARY是同义词
> PURGE {MASTER | BINARY} LOGS TO 'log_name'
> PURGE {MASTER | BINARY} LOGS BEFORE 'date'
删除指定的日志或日期之前的日志索引中的所有二进制日志。这些日志也会从记录在日志索引文件中的清单中被删除MySQL BIN-LOG 日志，这样被给定的日志成为第一个。

实例：
> PURGE MASTER LOGS TO 'MySQL-bin.010'; //清除MySQL-bin.010日志
> PURGE MASTER LOGS BEFORE '2008-06-22 13:00:00';  //清除2008-06-22 13:00:00前binlog日志
> PURGE MASTER LOGS BEFORE DATE_SUB( NOW( ), INTERVAL 3 DAY); //清除3天前binlog日志BEFORE，变量的date自变量可以为'YYYY-MM-DD hh:mm:ss'格式。
-----------------------------------------------------
5）清除binlog时，对从mysql的影响
如果有一个活跃的slave从属服务器，该服务器当前正在读取您正在试图删除的日志之一，则本语句不会起作用，而是会失败，并伴随一个错误；不过如果slave从属服务器是关闭的（或master-slave主从关系关闭），并且碰巧清理了其想要读取的日志之一，则slave从属服务器启动后不能复制；当从属服务器正在复制时，本语句可以安全运行，不需要停止它们。

6）binglog的查看
通过mysqlbinlog命令可以查看binlog的内容
[root@localhost ~]# mysqlbinlog /home/mysql/binlog/binlog.000003 | more
/*!40019 SET @@session.max_insert_delayed_threads=0*/;
/*!50003 SET @OLD_COMPLETION_TYPE=@@COMPLETION_TYPE,COMPLETION_TYPE=0*/;
DELIMITER /*!*/;
# at 4
#120330 16:51:46 server id 1 end_log_pos 98 Start: binlog v 4, server v 5.0.45-log created 120330 1
6:51:46
# Warning: this binlog was not closed properly. Most probably mysqld crashed writing it.
# at 196
#120330 17:54:15 server id 1 end_log_pos 294 Query thread_id=3 exec_time=2 error_code=0
SET TIMESTAMP=1333101255/*!*/;
insert into tt7 select * from tt7/*!*/;
# at 294
#120330 17:54:46 server id 1 end_log_pos 388 Query thread_id=3 exec_time=28 error_code=0
SET TIMESTAMP=1333101286/*!*/;
alter table tt7 engine=innodb/*!*/;

解析binlog格式：

位置
位于文件中的位置，“at 196”说明“事件”的起点，是以第196字节开始；“end_log_pos 294”说明以第294字节结束
时间戳
事件发生的时间戳：“120330 17:54:46”
事件执行时间
事件执行花费的时间:"exec_time=28"
错误码
错误码为：“error_code=0”
服务器的标识
服务器的标识id：“server id 1”

注意下面几点：
1.mysql的日志切不可想象是可以恢复到任何时间的状态，这个恢复是有前提的！
至少得有一个从日志记录开始后的数据库备份，通过日志恢复数据库实际上只是一个对以前操作的回放过程而已，不用想得太复杂。
既然是回放操作，那么就得注意了，如果是执行了两次恢复那就相当于是回放了两次，后果可想而知。
所以：

1）恢复前务必先备份数据.

2）由于二进制文件多,并且需要恢复的数据跨度大,可以考虑将日志文件合并在恢复.

2. 开启binlog日志功能
要想通过日志恢复数据库，必须首先在my.cnf文件里定义，log-bin=mysql-bin，这样产生的binlog日志名就是以mysql-bin命名的

3.什么时候会生成新的binlog文件
1）在备份的时候加入--flush-logs
2）重新启动mysql服务的时候
特别提示，mysql每次启动都会重新生成一个类似mysql-bin.00000n的文件，如果你的mysql每天都要重新启动一次的话，这时候你就要特别注意不要选错日志文件了。

### 二、binlog日志格式介绍

（1）Mysql binlog日志有三种格式，分别是Statement、MiXED、ROW

1）Statement：每一条会修改数据的sql都会记录在binlog中
优点：不需要记录每一行的变化，减少了binlog日志量，节约了IO，提高性能。(相比row能节约多少性能与日志量，这个取决于应用的SQL情况，正常同一条记录修改或者插入row格式所产生的日志量还小于Statement产生的日志量，但是考虑到如果带条件的update操作，以及整表删除，alter表等操作，ROW格式会产生大量日志，因此在考虑是否使用ROW格式日志时应该跟据应用的实际情况，其所产生的日志量会增加多少，以及带来的IO性能问题。)
缺点：由于记录的只是执行语句，为了这些语句能在slave上正确运行，因此还必须记录每条语句在执行的时候的一些相关信息，以保证所有语句能在slave得到和在master端执行时候相同 的结果。另外mysql 的复制,像一些特定函数功能，slave可与master上要保持一致会有很多相关问题(如sleep()函数， last_insert_id()，以及user-defined functions(udf)会出现问题).

使用以下函数的语句也无法被复制：
* LOAD_FILE()
* UUID()
* USER()
* FOUND_ROWS()
* SYSDATE() (除非启动时启用了 --sysdate-is-now 选项)

同时在INSERT ...SELECT 会产生比 RBR 更多的行级锁

2）Row:不记录sql语句上下文相关信息，仅保存哪条记录被修改
优点： binlog中可以不记录执行的sql语句的上下文相关的信息，仅需要记录那一条记录被修改成什么了。所以rowlevel的日志内容会非常清楚的记录下每一行数据修改的细节。而且不会出现某些特定情况下的存储过程，或function，以及trigger的调用和触发无法被正确复制的问题
缺点:所有的执行的语句当记录到日志中的时候，都将以每行记录的修改来记录，这样可能会产生大量的日志内容,比如一条update语句，修改多条记录，则binlog中每一条修改都会有记录，这样造成binlog日志量会很大，特别是当执行alter table之类的语句的时候，由于表结构修改，每条记录都发生改变，那么该表每一条记录都会记录到日志中。

3）Mixedlevel: 是以上两种level的混合使用，一般的语句修改使用statment格式保存binlog，如一些函数，statement无法完成主从复制的操作，则采用row格式保存binlog,MySQL会根据执行的每一条具体的sql语句来区分对待记录的日志形式，也就是在Statement和Row之间选择一种.新版本的MySQL中队row level模式也被做了优化，并不是所有的修改都会以row level来记录，像遇到表结构变更的时候就会以statement模式来记录。至于update或者delete等修改数据的语句，还是会记录所有行的变更。

Mixed日志说明：
在slave日志同步过程中，对于使用now这样的时间函数，MIXED日志格式，会在日志中产生对应的unix_timestamp()*1000的时间字符串，slave在完成同步时，取用的是sqlEvent发生的时间来保证数据的准确性。另外对于一些功能性函数slave能完成相应的数据同步，而对于上面指定的一些类似于UDF函数，导致Slave无法知晓的情况，则会采用ROW格式存储这些Binlog，以保证产生的Binlog可以供Slave完成数据同步。

（2）binlog基本配制与格式设定
1）基本配制
binlog日志格式可以通过mysql的my.cnf文件的属性binlog_format指定。如以下：
binlog_format = MIXED              //binlog日志格式
log_bin =目录/mysql-bin.log       //binlog日志名
expire_logs_days = 7                 //binlog过期清理时间
max_binlog_size 100m              //binlog每个日志文件大小

binlog-do-db=需要备份的数据库名，如果备份多个数据库，重复设置这个选项即可
binlog-ignore-db=不需要备份的数据库苦命，如果备份多个数据库，重复设置这个选项即可

2）Binlog日志格式选择
Mysql默认是使用Statement日志格式，推荐使用MIXED.
由于一些特殊使用，可以考虑使用ROWED，如自己通过binlog日志来同步数据的修改，这样会节省很多相关操作。对于binlog数据处理会变得非常轻松,相对mixed，解析也会很轻松(当然前提是增加的日志量所带来的IO开销在容忍的范围内即可)。

3）mysqlbinlog格式选择
mysql对于日志格式的选定原则:如果是采用 INSERT，UPDATE，DELETE 等直接操作表的情况，则日志格式根据 binlog_format 的设定而记录,如果是采用 GRANT，REVOKE，SET PASSWORD 等管理语句来做的话，那么无论如何 都采用 SBR 模式记录

（3）Mysql Binlog日志分析

通过MysqlBinlog指令查看具体的mysql日志，如下:
```sql
///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////

SET TIMESTAMP=1350355892/*!*/;

BEGIN

/*!*/;

# at 1643330

#121016 10:51:32 server id 1 end_log_pos 1643885 Query thread_id=272571 exec_time=0 error_code=0

SET TIMESTAMP=1350355892/*!*/;

Insert into T_test….)

/*!*/;

# at 1643885

#121016 10:51:32 server id 1 end_log_pos 1643912 Xid = 0

COMMIT/*!*/;

///////////////////////////////////////////////////////////////////////////////////////////////////////////////////////////
```
1.开始事物的时间:

SET TIMESTAMP=1350355892/*!*/;

BEGIN

2.sqlevent起点

#at 1643330 :为事件的起点，是以1643330字节开始。

3.sqlevent 发生的时间点

#121016 10:51:32:是事件发生的时间，

4.serverId

server id 1 :为master 的serverId

5.sqlevent终点及花费时间，错误码

end_log_pos 1643885:为事件的终点，是以1643885 字节结束。

execTime 0: 花费的时间

error_code=0:错误码

Xid:事件指示提交的XA事务

### 三、mysql日志（重点binlog日志）的优化说明
MySQL系统的伸缩性很强，既可以在充足的硬件资源环境下高效运行，也可以在极少资源环境下很好的运行，
但不管怎样，尽可能充足的硬件资源对MySQL的性能提升总是有帮助的。

下面着重分析一下MySQL的日志（主要是Binlog）对系统性能的影响，并根据日志的相关特性得出相应的优化思路。

1）日志产生的性能影响
由于日志的记录带来的直接性能损耗就是数据库系统中最为昂贵的IO资源。

MySQL的日志主要包括错误日志（ErrorLog），更新日志（UpdateLog），二进制日志（Binlog），查询日志（QueryLog），慢查询日志（SlowQueryLog）等。
特别注意：更新日志是老版本的MySQL才有的，目前已经被二进制日志替代。

在默认情况下，系统仅仅打开错误日志，关闭了其他所有日志，以达到尽可能减少IO损耗提高系统性能的目的。
但是在一般稍微重要一点的实际应用场景中，都至少需要打开二进制日志，因为这是MySQL很多存储引擎进行增量备份的基础，也是MySQL实现复制的基本条件。
有时候为了进一步的mysql性能优化，定位执行较慢的SQL语句，很多系统也会打开慢查询日志来记录执行时间超过特定数值（由我们自行设置）的SQL语句。

一般情况下，在生产系统中很少有系统会打开查询日志。因为查询日志打开之后会将MySQL中执行的每一条Query都记录到日志中，会该系统带来比较大的IO负担，而带来的实际效益却并不是非常大。一般只有在开发测试环境中，为了定位某些功能具体使用了哪些SQL语句的时候，才会在短时间段内打开该日志来做相应的分析。
所以，在MySQL系统中，会对性能产生影响的MySQL日志（不包括各存储引擎自己的日志）主要就是Binlog了。

2）Binlog 相关参数及优化策略
我们首先看看Binlog的相关参数，通过执行如下命令可以获得关于Binlog的相关参数。
当然，其中也显示出了“innodb_locks_unsafe_for_binlog”这个Innodb存储引擎特有的与Binlog相关的参数：
```sql
mysql> show variables like '%binlog%';
+-----------------------------------------+----------------------+
| Variable_name              | Value        |
+-----------------------------------------+----------------------+
| binlog_cache_size            | 16777216       |
| binlog_checksum             | CRC32        |
| binlog_direct_non_transactional_updates | OFF         |
| binlog_error_action           | IGNORE_ERROR     |
| binlog_format              | MIXED        |
| binlog_gtid_simple_recovery       | OFF         |
| binlog_max_flush_queue_time       | 0          |
| binlog_order_commits          | ON          |
| binlog_row_image            | FULL         |
| binlog_rows_query_log_events      | OFF         |
| binlog_stmt_cache_size         | 32768        |
| binlogging_impossible_mode       | IGNORE_ERROR     |
| innodb_api_enable_binlog        | OFF         |
| innodb_locks_unsafe_for_binlog     | OFF         |
| max_binlog_cache_size          | 18446744073709547520 |
| max_binlog_size             | 1073741824      |
| max_binlog_stmt_cache_size       | 18446744073709547520 |
| simplified_binlog_gtid_recovery     | OFF         |
| sync_binlog               | 1          |
+-----------------------------------------+----------------------+
19 rows in set (0.00 sec)
```
“binlog_cache_size"：在事务过程中容纳二进制日志SQL语句的缓存大小。二进制日志缓存是服务器支持事务存储引擎并且服务器启用了二进制日志(—log-bin选项)的前提下为每个客户端分配的内存，注意，是每个Client都可以分配设置大小的binlogcache空间。如果读者朋友的系统中经常会出现多语句事务的华，可以尝试增加该值的大小，以获得更有的性能。当然，我们可以通过MySQL的以下两个状态变量来判断当前的binlog_cache_size的状况：Binlog_cache_use和Binlog_cache_disk_use。

“max_binlog_cache_size”：和"binlog_cache_size"相对应，但是所代表的是binlog能够使用的最大cache内存大小。当我们执行多语句事务的时候，max_binlog_cache_size如果不够大的话，系统可能会报出“Multi-statementtransactionrequiredmorethan'max_binlog_cache_size'bytesofstorage”的错误。

“max_binlog_size”：Binlog日志最大值，一般来说设置为512M或者1G，但不能超过1G。该大小并不能非常严格控制Binlog大小，尤其是当到达Binlog比较靠近尾部而又遇到一个较大事务的时候，系统为了保证事务的完整性，不可能做切换日志的动作，只能将该事务的所有SQL都记录进入当前日志，直到该事务结束。这一点和Oracle的Redo日志有点不一样，因为Oracle的Redo日志所记录的是数据文件的物理位置的变化，而且里面同时记录了Redo和Undo相关的信息，所以同一个事务是否在一个日志中对Oracle来说并不关键。而MySQL在Binlog中所记录的是数据库逻辑变化信息，MySQL称之为Event，实际上就是带来数据库变化的DML之类的Query语句。

“sync_binlog”：这个参数是对于MySQL系统来说是至关重要的，他不仅影响到Binlog对MySQL所带来的性能损耗，而且还影响到MySQL中数据的完整性。对于“sync_binlog”参数的各种设置的说明如下：

sync_binlog=0，当事务提交之后，MySQL不做fsync之类的磁盘同步指令刷新binlog_cache中的信息到磁盘，而让Filesystem自行决定什么时候来做同步，或者cache满了之后才同步到磁盘。

sync_binlog=n，当每进行n次事务提交之后，MySQL将进行一次fsync之类的磁盘同步指令来将binlog_cache中的数据强制写入磁盘。

在MySQL中系统默认的设置是sync_binlog=0，也就是不做任何强制性的磁盘刷新指令，这时候的性能是最好的，但是风险也是最大的。因为一旦系统Crash，在binlog_cache中的所有binlog信息都会被丢失。而当设置为“1”的时候，是最安全但是性能损耗最大的设置。因为当设置为1的时候，即使系统Crash，也最多丢失binlog_cache中未完成的一个事务，对实际数据没有任何实质性影响。从以往经验和相关测试来看，对于高并发事务的系统来说，“sync_binlog”设置为0和设置为1的系统写入性能差距可能高达5倍甚至更多。

另：
MySQL的复制（Replication），实际上就是通过将Master端的Binlog通过利用IO线程通过网络复制到Slave端，然后再通过SQL线程解析Binlog中的日志再应用到数据库中来实现的。所以，Binlog量的大小对IO线程以及Msater和Slave端之间的网络都会产生直接的影响。

MySQL中Binlog的产生量是没办法改变的，只要我们的Query改变了数据库中的数据，那么就必须将该Query所对应的Event记录到Binlog中。那我们是不是就没有办法优化复制了呢？当然不是，在MySQL复制环境中，实际上是是有8个参数可以让我们控制需要复制或者需要忽略而不进行复制的DB或者Table的，分别为：

Binlog_Do_DB：设定哪些数据库（Schema）需要记录Binlog；

Binlog_Ignore_DB：设定哪些数据库（Schema）不要记录Binlog；

Replicate_Do_DB：设定需要复制的数据库（Schema），多个DB用逗号（“,”）分隔；

Replicate_Ignore_DB：设定可以忽略的数据库（Schema）；

Replicate_Do_Table：设定需要复制的Table；

Replicate_Ignore_Table：设定可以忽略的Table；

Replicate_Wild_Do_Table：功能同Replicate_Do_Table，但可以带通配符来进行设置；

Replicate_Wild_Ignore_Table：功能同Replicate_Ignore_Table，可带通配符设置；


通过上面这八个参数，我们就可以非常方便按照实际需求，控制从Master端到Slave端的Binlog量尽可能的少，从而减小Master端到Slave端的网络流量，减少IO线程的IO量，还能减少SQL线程的解析与应用SQL的数量，最终达到改善Slave上的数据延时问题。

实际上，上面这八个参数中的前面两个是设置在Master端的，而后面六个参数则是设置在Slave端的。虽然前面两个参数和后面六个参数在功能上并没有非常直接的关系，但是对于优化MySQL的Replication来说都可以启到相似的功能。当然也有一定的区别，其主要区别如下：

如果在Master端设置前面两个参数，不仅仅会让Master端的Binlog记录所带来的IO量减少，还会让Master端的IO线程就可以减少Binlog的读取量，传递给Slave端的IO线程的Binlog量自然就会较少。这样做的好处是可以减少网络IO，减少Slave端IO线程的IO量，减少Slave端的SQL线程的工作量，从而最大幅度的优化复制性能。当然，在Master端设置也存在一定的弊端，因为MySQL的判断是否需要复制某个Event不是根据产生该Event的Query所更改的数据


所在的DB，而是根据执行Query时刻所在的默认Schema，也就是我们登录时候指定的DB或者运行“USEDATABASE”中所指定的DB。只有当前默认DB和配置中所设定的DB完全吻合的时候IO线程才会将该Event读取给Slave的IO线程。所以如果在系统中出现在默认DB和设定需要复制的DB不一样的情况下改变了需要复制的DB中某个Table的数据的时候，该Event是不会被复制到Slave中去的，这样就会造成Slave端的数据和Master的数据不一致的情况出现。同样，如果在默认Schema下更改了不需要复制的Schema中的数据，则会被复制到Slave端，当Slave端并没有该Schema的时候，则会造成复制出错而停止。

而如果是在Slave端设置后面的六个参数，在性能优化方面可能比在Master端要稍微逊色一点，因为不管是需要还是不需要复制的Event都被会被IO线程读取到Slave端，这样不仅仅增加了网络IO量，也给Slave端的IO线程增加了RelayLog的写入量。但是仍然可以减少Slave的SQL线程在Slave端的日志应用量。虽然性能方面稍有逊色，但是在Slave端设置复制过滤机制，可以保证不会出现因为默认Schema的问题而造成Slave和Master数据不一致或者复制出错的问题。

3）慢查询日志Query Log 相关参数及使用建议
再来看看SlowQueryLog的相关参数配置。有些时候，我们为了定位系统中效率比较地下的Query语句，则需要打开慢查询日志，也就是SlowQueryLog。我们可以如下查看系统慢查询日志的相关设置：

```mysql
mysql> show variables like 'log_slow%';
+------------------+-------+
| Variable_name | Value |
+------------------+-------+
| log_slow_queries | ON |
+------------------+-------+
1 row in set (0.00 sec)

mysql> show variables like 'long_query%';
+-----------------+-------+
| Variable_name | Value |
+-----------------+-------+
| long_query_time | 1 |
+-----------------+-------+
1 row in set (0.01 sec)
```
“log_slow_queries”参数显示了系统是否已经打开SlowQueryLog功能，而“long_query_time”参数则告诉我们当前系统设置的SlowQuery记录执行时间超过多长的Query。在MySQLAB发行的MySQL版本中SlowQueryLog可以设置的最短慢查询时间为1秒，这在有些时候可能没办法完全满足我们的要求，如果希望能够进一步缩短慢查询的时间限制，可以使用Percona提供的microslow-patch（件成为mslPatch）来突破该限制。mslpatch不仅仅能将慢查询时间减小到毫秒级别，同时还能通过一些特定的规则来过滤记录的SQL，如仅记录涉及到某个表的SlowQuery等等附加功能。

打开SlowQueryLog功能对系统性能的整体影响没有Binlog那么大，毕竟SlowQueryLog的数据量比较小，带来的IO损耗也就较小，但是，系统需要计算每一条Query的执行时间，所以消耗总是会有一些的，主要是CPU方面的消耗。如果大家的系统在CPU资源足够丰富的时候，可以不必在乎这一点点损耗，毕竟他可能会给我们带来更大性能优化的收获。但如果我们的CPU资源也比较紧张的时候，也完全可以在大部分时候关闭该功能，而只需要间断性的打开SlowQueryLog功能来定位可能存在的慢查询。

MySQL的其他日志由于使用很少（QueryLog）或者性能影响很少，在此就不做过多分析了。

以上这篇Mysql数据库之Binlog日志使用总结(必看篇)就是小编分享给大家的全部内容了，希望能给大家一个参考，也希望大家多多支持脚本之家。