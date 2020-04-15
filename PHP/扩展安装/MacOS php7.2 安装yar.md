MacOS php7.2 安装yar
---
### MacOS php7.2 安装yar
#### yar依赖msgpack扩展
```sh
axel https://pecl.php.net/get/msgpack-2.0.3.tgz
tar -zxvf msgpack-2.0.3.tgz
cd msgpack-2.0.3
/usr/bin/phpize
./configure
make && make install
```

#### yar 安装
```sh
axel http://pecl.php.net/get/yar-2.0.4.tgz
tar xvzf yar-2.0.4.tgz
cd yar-2.0.4/
phpize
./configure
make && make install

vim /etc/php.ini 加上extension=yar.so
```

#### 查看支持的配置：pec
```sh
php --re yar
```