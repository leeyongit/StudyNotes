Centos+OpenResty+PHP+MariaDB环境搭建
==================================

CentOS
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

PATH=/usr/local/openresty/nginx/sbin:$PATH
export PATH

nginx -p /root/work/ -c conf/nginx.conf
```
```
log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                  '$status $body_bytes_sent "$http_referer" '
                  '"$http_user_agent" "$http_x_forwarded_for" "$request_time"  "$upstream_response_time"';
```
```sh
groupadd www
useradd -m -s /sbin/nologin -g www www
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
首先，安装额外包括 PHP v7.2 的 CentOS repo :
```
wget http://rpms.remirepo.net/enterprise/remi-release-7.rpm
rpm -Uvh remi-release-7.rpm
```
使 php72 repository 生效（默认不生效）:
```
yum install yum-utils -y
yum-config-manager --enable remi-php72
```
然后安装 PHP package:
```
yum --enablerepo=remi,remi-php72 install php-fpm php-common
```
安装通用模块:
```
yum --enablerepo=remi,remi-php72 install php-opcache php-pecl-apcu php-cli php-pear php-pdo php-mysqlnd php-pgsql php-pecl-mongodb php-pecl-redis php-pecl-memcache php-pecl-memcached php-gd php-mbstring php-mcrypt php-xml
```
## 安装swoole
yum install php-pecl-swoole4 php-pecl-swoole4-devel

## 安装rdkafka
yum install php-pecl-rdkafka

## 阿里云ecs-admin
```sh
yum --enablerepo=remi,remi-php72 install php-opcache php-pecl-apcu php-cli php-pear php-pdo php-mysqlnd php-pgsql php-pecl-redis php-gd php-mbstring php-mcrypt php-xml
```

```sh
systemctl start php-fpm.service
systemctl restart php-fpm.service
systemctl enable php-fpm.service
```

## 配置 Nginx 和 PHP 7.2 匹配
mkdir -p /var/log/nginx
mkdir -p /var/log/php-fmp
/usr/sbin/php-fpm --fpm-config php-fpm-goeasy.dev.conf
```s
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
        fastcgi_pass unix:/var/run/php-fpm/php-fpm-vhost.sock;
        fastcgi_index index.php;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }
}
```


## 安装 MariaDB

yum install mariadb-server mariadb -y

安装完成后，我们同样启动服务和配置服务自启动：

systemctl start mariadb
systemctl enable mariadb

然后, 运行初始化脚本，清除一些默认配置:(这时，需要你提供 MariaDB 的密码, 但这是第一次登陆，没有密码，因此直接按回车)

mysql_secure_installation