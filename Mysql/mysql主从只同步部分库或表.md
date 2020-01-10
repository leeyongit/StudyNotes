mysql主从只同步部分库或表
---
同步部分数据有两个思路，1.master只发送需要的；2.slave只接收想要的。

### master端：
```sh
binlog-do-db      二进制日志记录的数据库（多数据库用逗号，隔开）
binlog-ignore-db 二进制日志中忽略数据库 （多数据库用逗号，隔开）
```

举例说明：
1）binlog-do-db=YYY 需要同步的数据库，不在内的不同步。（不添加这行表示同步所有）

2）binlog-ignore-db = mysql  这是不记录binlog，来达到从库不同步mysql库，以确保各自权限
```sh
     binlog-ignore-db = performance_schema
     binlog-ignore-db = information_schema
```

### slave端
```sh
replicate-do-db    设定需要复制的数据库（多数据库使用逗号，隔开）
replicate-ignore-db 设定需要忽略的复制数据库 （多数据库使用逗号，隔开）
replicate-do-table  设定需要复制的表
replicate-ignore-table 设定需要忽略的复制表 
replicate-wild-do-table 同replication-do-table功能一样，但是可以通配符
replicate-wild-ignore-table 同replication-ignore-table功能一样，但是可以加通配符
```
增加通配符的两个配置
replicate-wild-do-table=db_name.%   只复制哪个库的哪个表
replicate-wild-ignore-table=mysql.%   忽略哪个库的哪个表