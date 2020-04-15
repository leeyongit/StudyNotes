Redis扩展安装
---

### 安装Redis php扩展前先安装redis-3.0.0
```php
cd /usr/local/src
sudo wget http://download.redis.io/releases/redis-3.0.0.tar.gz
sudo tar -zxvf redis-3.0.0.tar.gz
sudo cd redis-3.0.0
#如果不加参数,linux下会可能报错
sudo make MALLOC=libc
sudo make install
####启动redis
redis.conf修改：
redis-server /etc/redis/redis.conf
```
### Redis php扩展安装
```php
cd /usr/local/src
sudo wget http://pecl.php.net/get/redis-3.1.3.tgz
sudo tar -zxvf redis-3.1.3.tgz
sudo cd redis-3.1.3
sudo phpize
sudo ./configure --with-php-config=/usr/bin/php-config
sudo make
sudo make install
sudo vim /etc/php.ini 添加：extension=redis.so
```