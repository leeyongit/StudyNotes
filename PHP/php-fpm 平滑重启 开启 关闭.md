php-fpm 平滑重启 开启 关闭
=======================
INT, TERM
QUIT 平滑终止
USR1 重新打开日志文件
USR2 平滑重载所有worker进程并重新载入配置和二进制模块

## 示例

启动
```
/usr/sbin/php-fpm
/usr/sbin/php-fpm --fpm-config /etc/php-fpm.d/php-fshd.fensihudong.com.conf
```
查看php-fpm进程数：
```
ps aux | grep -c php-fpm
```
查看php-fpm的master进程号[pif]
```
ps aux|grep 'php-fpm: master'|grep -v grep|awk '{print $2}'
```
查看php-fpm的master进程号
```
cat /run/php-fpm.pid
```
强制关闭
```
pkill php-fpm

kill -INT `cat /run/php-fpm.pid`
kill -INT [pid]
```
平滑重启
```
kill -USR2 `cat /run/php-fpm.pid`
```
或者
```
kill -USR2 [pid]
```