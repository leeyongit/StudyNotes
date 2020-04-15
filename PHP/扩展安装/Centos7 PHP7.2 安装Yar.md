Centos7 PHP7.2 安装Yar
---
### Centos7 PHP7.2 安装Yar

```sh
yum install php-devel php-pear httpd-devel
yum -y install libcurl-devel
pecl install msgpack
pecl install yar
/注意PHP7,要使用yar-2.0.2
$ sudo ./pecl install yar-2.0.2 //注意yar-2.0.2版本
```

### Centos7 PHP5.6 安装Yar
#### yar依赖msgpack扩展

```sh
wget https://github.com/msgpack/msgpack-php/archive/msgpack-0.5.7.tar.gz
tar zxvf msgpack-0.5.7.tar.gz
cd msgpack-php-msgpack-0.5.7/
phpize
./configure --with-php-config=/usr/bin/php-config
make && make install
```
安装成功，然后把msgpack.so加到php.ini里，重启php，完成安装

#### yar 安装
```sh
wget http://pecl.php.net/get/yar-1.2.5.tgz
tar zxvf yar-1.2.5.tgz
cd yar-1.2.5
phpize
./configure --with-php-config=/usr/bin/php-config
make && make install
```
#### 报错

> configure: error: Please reinstall the libcurl distribution - easy.h should be in /include/curl/
```sh
yum -y install libcurl-devel
```