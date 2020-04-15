## Zookeeper安装
安装Zookeeper php扩展前先安装zookeeper-3.4.6
```php
cd /usr/local/src
sudo wget http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.10/zookeeper-3.4.10.tar.gz
sudo tar -zxvf zookeeper-3.4.10.tar.gz
cd zookeeper-3.4.10/src/c
sudo ./configure --prefix=/usr/local
sudo make
sudo make install
```
## Zookeeper php扩展安装
```php
cd /usr/local/src
sudo wget http://pecl.php.net/get/zookeeper-0.5.0.tgz
sudo tar -xvf zookeeper-0.5.0.tgz
cd zookeeper-0.5.0
sudo phpize
sudo ./configure --with-php-config=/usr/bin/php-config
sudo make
sudo make install
sudo vim /etc/php.ini 添加：extension=zookeeper.so
```