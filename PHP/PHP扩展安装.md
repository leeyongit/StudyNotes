# PHP扩展安装

### 为PHP安装zip扩展
**第一步，下载源代码并解压缩**
```bash
wget http://pecl.php.net/get/zip-1.10.2.tgz
tar zxvf zip-1.10.2.tgz
cd zip-1.10.2
```
**第二步，编译并安装**
```bash
/usr/local/php/bin/phpize
#运行此命令后，会自动生成几个文件，包括configure文件
./configure --with-php-config=/usr/local/php/bin/php-config
make & make install
```
**第三步，配置php.ini，并开启zip扩展**
zip安装后，会显示安装的路径，比如：/usr/local/php/lib/php/extensions/zip.so
在php.ini中加入： extension=/usr/local/php5/lib/php/extensions/zip.so
如果是LNMP安装包，需要重新启动php-fpm，如果是LNAMP结构，需要重启apache即可。
至此，安装zip扩展完毕，可以在phpinfo()中检查zip扩展是否已经正确启用。

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