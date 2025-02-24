## PHP接口响应时间慢优化

这种情况通常需要从几个层面来分析，因为响应慢不一定是 PHP 执行或 MySQL 查询慢导致的：

1. 网络层面：
```bash
# 检查网络延迟
ping 服务器IP

# 检查网络连接状态
netstat -n | awk '/^tcp/ {++state[$NF]} END {for(key in state) print key,"\t",state[key]}'

# 查看具体连接
netstat -natp
```

2. 系统资源：
```bash
# 检查系统负载
top
vmstat 1

# 检查内存使用
free -m

# 检查磁盘IO
iostat -x 1
```

3. PHP-FPM 配置检查：
```bash
# 查看 php-fpm 进程数
ps aux | grep php-fpm | wc -l

# 检查配置
pm = dynamic
pm.max_children = 50      # 可能太少
pm.start_servers = 5
pm.min_spare_servers = 5
pm.max_spare_servers = 35
pm.max_requests = 500
```

4. 可能的原因：
- PHP-FPM 进程数不足，请求在排队
- 系统资源（CPU/内存）不足
- 网络带宽限制
- TCP 连接数达到限制
- 磁盘 IO 瓶颈

5. 建议增加的监控：
```bash
# 对于 PHP-FPM
pm.status_path = /status    # 开启状态页面

# 对于 MySQL
SHOW PROCESSLIST;          # 查看当前连接
SHOW STATUS;               # 查看状态变量
```

6. 调试方法：
```bash
# 在压测时实时监控
watch -n 1 'ps aux | grep php-fpm | wc -l'   # PHP-FPM进程数
watch -n 1 'netstat -an | grep ESTABLISHED | wc -l'  # TCP连接数

# 抓包分析
tcpdump -i any port 80 -w debug.cap
```

7. 优化思路：
- 增加 PHP-FPM 进程数
- 优化系统参数（如 ulimit）
- 增加服务器资源
- 添加负载均衡
- 引入缓存层

