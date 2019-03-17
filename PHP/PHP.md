# PHP 
---
> 本机php.ini位置
```sh
/Applications/MAMP/bin/php/php7.2.10/conf/php.ini
```
> 查找php-config位置
```sh
which php-config
```

## MACOS PHP7.2 设置 
```sh
export PATH=/usr/local/php5/bin:$PATH
export PATH=/Applications/MAMP/bin/php/php7.2.10/bin:$PATH
/Applications/MAMP/bin/php/php7.2.10/lib/php/extensions/no-debug-non-zts-20170718/rdkafka.so
```

# Yar 扩展安装
---

## MacOS php7.2 安装yar
### yar依赖msgpack扩展
```sh
axel https://pecl.php.net/get/msgpack-2.0.3.tgz
tar -zxvf msgpack-2.0.3.tgz
cd msgpack-2.0.3
/usr/bin/phpize
./configure
make && make install
```
Installing shared cd :     /Applications/MAMP/bin/php/php7.2.10/lib/php/extensions/no-debug-non-zts-20170718/
Installing header files:   /Applications/MAMP/bin/php/php7.2.10/include/php/

### yar 安装
```sh
axel http://pecl.php.net/get/yar-2.0.4.tgz
tar xvzf yar-2.0.4.tgz
cd yar-2.0.4/
phpize
./configure
make && make install

vim /etc/php.ini 加上extension=yar.so
```
查看支持的配置：pec
php --re yar

## Centos7 PHP7.2 安装Yar
```sh
yum install php-devel php-pear httpd-devel
yum -y install libcurl-devel
pecl install msgpack
pecl install yar
/注意PHP7,要使用yar-2.0.2
$ sudo ./pecl install yar-2.0.2 //注意yar-2.0.2版本
```
## Centos7 PHP5.6 安装Yar
### yar依赖msgpack扩展
```sh
wget https://github.com/msgpack/msgpack-php/archive/msgpack-0.5.7.tar.gz
tar zxvf msgpack-0.5.7.tar.gz
cd msgpack-php-msgpack-0.5.7/
phpize
./configure --with-php-config=/usr/bin/php-config
make && make install
```
安装成功，然后把msgpack.so加到php.ini里，重启php，完成安装

### yar 安装
```sh
wget http://pecl.php.net/get/yar-1.2.5.tgz
tar zxvf yar-1.2.5.tgz 
cd yar-1.2.5
phpize
./configure --with-php-config=/usr/bin/php-config
make && make install
```


### 报错
> configure: error: Please reinstall the libcurl distribution - easy.h should be in /include/curl/
```sh
yum -y install libcurl-devel
```