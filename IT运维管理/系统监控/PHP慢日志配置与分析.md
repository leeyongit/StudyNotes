## PHP慢日志配置与分析

PHP 慢日志配置主要通过修改 php.ini 文件来实现。以下是主要配置步骤：

1. 定位 php.ini 文件：
```bash
# 查看 php.ini 位置
php -i | grep "php.ini"
```

2. 主要配置项：
```ini
; 开启慢日志
xdebug.profiler_enable = 1

; 设置慢日志路径
xdebug.profiler_output_dir = "/path/to/logs"

; 设置慢请求时间阈值（单位：秒）
xhprof.sampling_interval = 100000

; PHP-FPM 慢日志配置
request_slowlog_timeout = 1
slowlog = /path/to/php-fpm/slow.log

; 开启错误日志
log_errors = On
error_log = /path/to/error.log
```

3. PHP-FPM 配置文件（www.conf）中的相关设置：
```ini
; 开启慢日志
request_slowlog_timeout = 1s

; 设置慢日志路径
slowlog = /var/log/php-fpm/slow.log

; 设置请求开始记录时间
request_slowlog_trace_depth = 20
```

4. 修改完配置后需要重启 PHP-FPM：
```bash
# 重启 PHP-FPM
systemctl restart php-fpm

# 或者
service php-fpm restart
```

5. 慢日志分析：
```bash
# 查看最新的慢日志
tail -f /var/log/php-fpm/slow.log

# 统计请求数最多的 URL
cat /var/log/php-fpm/slow.log | grep "script_filename" | sort | uniq -c | sort -nr | head -n 10

cat /www/server/php/74/var/log/slow.log | grep "script_filename" | sort | uniq -c | sort -nr | head -n 10

```

建议：
1. 生产环境建议设置合理的 request_slowlog_timeout 值，一般 1-3 秒
2. 定期清理或轮转日志文件
3. 可以使用工具如 XHProf 进行更详细的性能分析

如果需要更详细的性能分析，可以考虑：
1. XHProf
2. Xdebug
3. Tideways
4. New Relic

这些工具可以提供更详细的性能数据和分析报告。