# Centos常用命令

### 常用命令
```sh
cat /etc/redhat-release
uname -a  # 查看内核/操作系统/CPU信息
head -n 1 /etc/issue  #查看操作系统版本
cat /proc/cpuinfo  #查看CPU信息
hostname   #查看计算机名
ls | xargs rm -f # 删除当前目录下的文件
lsof -i:port  # 查看端口占用
du -sh * | sort -nr # 将当前目录下所有文件的大小给列出来,按照从大到小的方式排序
du -d 1 | sort -n -r| awk '{printf("   %.2f %s\t %s \n", $1/1024/1024, "G", $2)}'
```

### scp命令
```sh
scp local_file remote_username@remote_ip:remote_folder  # 从本地复制到远程
scp -r local_folder remote_username@remote_ip:remote_folder # 复制目录命令格式
```

### 监控网络状态信息
此命令用于显示整个系统目前的网络情况。例如目前的连接、数据包传递数据、或是路由表内容。
```sh
netstat -anp|more
```
### 追踪走向
```sh
traceroute
```
### 查看路由表
```sh
route
```
### 查看apache当前并发访问数：
```sh
netstat -an | grep ESTABLISHED | wc -l
```
### 查看网络占用端口号
```sh
netstat -n |grep 8081| awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}' 
netstat -n |grep 6379| awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'  
```

### 查看网络配置的命令
```sh
ifconfig # 查看所有网络接口的属性
iptables -L # 查看防火墙设置
route -n # 查看路由表
netstat -s # 查看网络统计信息
netstat -lntp # 查看所有监听端口
netstat -antp # 查看所有已经建立的连接
netstat -antp | wc -l # 查看所有已经建立的连接数量
netstat -antp | grep ESTABLISHED | wc -l # 查看活跃并发连接数
```
### 查看linux进程
```sh
ps -aux | grep node # 查看`node`进程
ps -ef # 查看所有进程
top # 实时显示进程状态
```
### 杀进程
```sh
killall -9 websocket # 干掉`websocket`服务进程
ps aux | grep mysql # 查看mysql进程
kill -9 35562 # 根据进程号杀
```
### 查看用户相关命令
```sh
useradd
groupadd
userdel
groupdel
w # 查看活动用户
id <用户名> # 查看指定用户信息
last # 查看用户登录日志
cut -d: -f1 /etc/passwd # 查看系统所有用户
cut -d: -f1 /etc/group # 查看系统所有组
crontab -l # 查看当前用户的计划任务
```
### log日志查看
```sh
cat /var/log/messages # 查询日志的全部内容
head -5 /var/log/messages # 查询日志的前5行
tail -5 /var/log/messages # 查询日志的最新5行
sed -n '5,10p' /var/log/messages # 查询日志的5到10行
```

### 磁盘挂载

第一步：列出所有已挂载磁盘 命令：

```sh
 disk -h
```

查看磁盘情况

```sh
fdisk -l
```

格式化分区

```sh
mkfs.ext3 /dev/vdb1
```

挂载分区

```sh
mount /dev/vdb /data
```

设置开机自动挂载：

```sh
echo '/dev/vdb /data ext3 defaults 0 0' >> /etc/fstab
```



