# CentOS 7安装最新 PHP 7.2，MySQL 5.7和Nginx


##  安装 OpenResty

你可以在你的 CentOS 系统中添加 openresty 仓库，这样就可以便于未来安装或更新我们的软件包（通过 yum update 命令）。运行下面的命令就可以添加我们的仓库：
```sh
sudo yum install yum-utils
sudo yum-config-manager --add-repo https://openresty.org/package/centos/openresty.repo
```
然后就可以像下面这样安装软件包，比如 openresty：
```sh
sudo yum install openresty
```
如果你想安装命令行工具 resty，那么可以像下面这样安装 openresty-resty 包：
```sh
sudo yum install openresty-resty
```
命令行工具 opm 在 openresty-opm 包里，而 restydoc 工具在 openresty-doc 包里头。

列出所有 openresty 仓库里头的软件包：
```sh
sudo yum --disablerepo="*" --enablerepo="openresty" list available
```
**加入PATH路径**
```sh
vim ~/.bash_profile
PATH=/usr/local/openresty/nginx/sbin:$PATH
export PATH
source ~/.bash_profile

```
这样可以直接用`ningx`命令了
**启动nginx**

```
nginx
# 或全路径启动
/usr/local/openresty/nginx/sbin/nginx
```
**指定配置文件启动**

```sh
nginx -p /root/work/ -c conf/nginx.conf
```
**nginx日志格式**
这个是参考不用配
```sh
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for" "$request_time"  "$upstream_response_time"';
```
```sh
groupadd www
useradd -M -s /sbin/nologin -g www www
```
    -s表示指定用户所用的shell，此处为/sbin/nologin，表示不登录。
    -m自动建立用户的登入目录。
    -M表示不创建用户主目录。
    -g表示指定用户的组名为www。
    最后的www表示用户名。
```sh
mkdir -p /home/{wwwroot,userdata}
chown -R www:www /home/wwwroot
```
## 安装 PHP v7.2
https://mirrors.tuna.tsinghua.edu.cn/remi/enterprise/7/php72/x86_64/repoview/
首先，安装额外包括 PHP v7.2 的 CentOS repo :
```sh
//wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm
wget http://rpms.remirepo.net/enterprise/7/remi/x86_64/remi-release-7.6-2.el7.remi.noarch.rpm
rpm -Uvh remi-release-7.rpm [--force --nodeps]
```
使 php72 repository 生效（默认不生效）:
```sh
yum install yum-utils -y
yum-config-manager --enable remi-php72
```
然后安装 PHP package:
```sh
yum --enablerepo=remi,remi-php72 install php-fpm php-common
```
安装通用模块:
```sh
yum --enablerepo=remi,remi-php72 install php-opcache php-pecl-apcu php-cli php-pear php-pdo php-mysqlnd php-pgsql php-pecl-mongodb php-pecl-redis php-pecl-memcache php-pecl-memcached php-gd php-mbstring php-mcrypt php-xml php-pecl-zip
```
### 安装swoole
```sh
yum install php-pecl-swoole4 php-pecl-swoole4-devel
```

### 安装rdkafka
```sh
yum install php-pecl-rdkafka
```

### Php-fpm 开启服务、重启服务、开机启动
```sh
systemctl start php-fpm.service
systemctl restart php-fpm.service
systemctl enable php-fpm.service
```

### php-fpm 配置多进程池运行

```
/usr/sbin/php-fpm --fpm-config php-fpm-vhost.conf
/usr/sbin/php-fpm --fpm-config php-fpm-vhost.conf -t //检查配置文件
/usr/sbin/php-fpm --fpm-config php-fpm-vhost.conf reload // 重新加载配置
```

PHP-FPM 的配置

```ini
[global]
pid = /run/php-fpm/php-fpm-vhost.pid
error_log = /var/log/php-fpm/error-vhost.log
log_level = error

[www]
listen = /var/run/php-fpm/php-fpm-vhost.sock
listen.backlog = -1
listen.allowed_clients = 127.0.0.1
user = www
group = www
listen.owner = www
listen.group = www
pm = static
pm.max_children = 2
pm.start_servers = 2
pm.min_spare_servers = 2
pm.max_spare_servers = 3
pm.max_requests = 2048
request_terminate_timeout = 120
request_slowlog_timeout = 5
slowlog = /var/log/php-fpm/slow-vhost.log
rlimit_files = 51200
rlimit_core = 0
catch_workers_output = yes
```


## 配置 Nginx 和 PHP 7.2 匹配

创建nginx、php日志目录

```sh
mkdir -p /var/log/nginx
mkdir -p /var/log/php-fmp
```

nginx 站点配置文件

```ini
server {
    listen   80;
    server_name  your_server_ip;
    # note that these lines are originally from the "location /" block
    root   /usr/share/nginx/html;
    index index.php index.html index.htm;
    location / {
        #try_files $uri $uri/ =404;
        try_files $uri $uri/ /index.php?q=$uri&$args;
    }
    error_page 404 /404.html;
    error_page 500 502 503 504 /50x.html;
    location = /50x.html {
        root /usr/share/nginx/html;
    }
    location ~ \.php$ {
        try_files $uri =404;
        fastcgi_pass unix:/var/run/php-fpm/php-fpm.sock;
        #fastcgi_pass unix:/var/run/php-fpm/php-fpm-vhost.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```



## 安装MySQL Server5.7

#### Step 1 — 寻找 CentOS 7 对应的 MySQL 社区版 repo

```sh
wget https://repo.mysql.com//mysql57-community-release-el7-11.noarch.rpm
rpm -ivh mysql57-community-release-el7-11.noarch.rpm
```

#### Step 2 — 安装 MySQL 社区版 server

```sh
yum install mysql-server
```

启动服务和自启动：

```sh
service mysqld start
sudo chkconfig  mysqld on
```

#### Step 3 — 查询默认 MySQL 密码并修改

```sh
sudo grep 'temporary password' /var/log/mysqld.log
```

显示类似如下：

```sh
[root@alibaba]# sudo grep 'temporary password' /var/log/mysqld.log
2017-11-12T07:38:36.716385Z 1 [Note] A temporary password is generated for root@localhost: r273_1qHr:ee
```

进入 MySQL 修改密码:

```sql
mysql -uroot -p
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'Daguanren.cc@2018';
```

#### Step 4 — 创建 MySQL 用户和赋予权限

创建一个 daguanren 的用户和 daguanrendb 的数据库，将数据库的所有操作权限赋予 daguanren 用户（下面的 % 代表从任何链接过来均可访问，* 代表所有表）

```sql
mysql> CREATE USER 'daguanren'@'%' IDENTIFIED BY 'Daguanren.cc@2018';

mysql> CREATE DATABASE IF NOT EXISTS daguanrendb DEFAULT CHARSET utf8 COLLATE utf8_general_ci;

mysql> GRANT ALL PRIVILEGES ON daguanrendb.* TO 'daguanren'@'%' IDENTIFIED BY 'Daguanren.cc@2018';

mysql> flush privileges;
```

#### Step 5 — MySQL 运维

**a、查看mysql安装状态**

```sh
sudo yum list installed | grep mysql

sudo rpm -qa | grep mysql*
```

**b、删除MySQL**

查找已安装的 MySQL

```vim
rpm -qa |grep -i mysql
```

删除 MySQL

```routeros
yum remove mysql*
```

查找已安装的 MySQL 目录

```routeros
find / -name mysql
```

删除 MySQL 目录

```dts
rm -rf /usr/share/mysql
```



## 安装 MariaDB

```sh
yum install mariadb-server mariadb -y
```

安装完成后，我们同样启动服务和配置服务自启动：

```sh
systemctl start mariadb
systemctl enable mariadb
```

然后, 运行初始化脚本，清除一些默认配置:(这时，需要你提供 MariaDB 的密码, 但这是第一次登陆，没有密码，因此直接按回车)

```sh
mysql_secure_installation
```



