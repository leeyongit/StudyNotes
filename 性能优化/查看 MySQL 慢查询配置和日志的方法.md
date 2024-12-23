## 查看 MySQL 慢查询配置和日志的方法

1. 查看慢查询是否开启及相关配置：
```sql
SHOW VARIABLES LIKE '%slow_query%';
SHOW VARIABLES LIKE '%long_query_time%';
```
主要参数说明：
- slow_query_log：是否开启慢查询日志(ON/OFF)
- slow_query_log_file：慢查询日志文件位置
- long_query_time：慢查询阈值(秒)，超过这个时间的查询会被记录

2. 查看慢查询日志是否生效：
```sql
SHOW GLOBAL STATUS LIKE '%Slow_queries%';
```

3. 修改配置的方法：

临时修改（重启后失效）：
```sql
SET GLOBAL slow_query_log = 'ON';
SET GLOBAL long_query_time = 1;
```

永久修改：在 my.cnf 配置文件中添加：
```ini
[mysqld]
slow_query_log = 1
slow_query_log_file = /var/lib/mysql/slow.log
long_query_time = 1
```

4. 分析慢查询日志的工具：
```bash
# 使用 mysqldumpslow 分析
mysqldumpslow /var/lib/mysql/slow.log

# 常用参数
-s: 排序方式
-t: 返回前几条
-g: 使用正则匹配模式
```

例如：
```bash
# 获取执行时间最长的10条慢查询
mysqldumpslow -s t -t 10 /var/lib/mysql/slow.log

# 获取返回记录数最多的10条慢查询
mysqldumpslow -s r -t 10 /var/lib/mysql/slow.log
```

需要注意的是：
1. 开启慢查询日志会对性能有一定影响
2. 生产环境建议设置合理的 long_query_time 值
3. 需要定期清理或轮转慢查询日志，避免占用过多磁盘空间