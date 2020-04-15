Swoole扩展安装
---
### 安装swoole

```sh
#!/bin/bash
pecl install swoole
```
**-bash: pecl: command not found的解决方法**
出现这个问题是因为没有安装php的扩展开发导致的，建议将php以及apache的开发都安装上，使用如下的命令。
```sh
yum install php-devel php-pear httpd-devel
yum install openssl
yum install openssl-devel
```

## Swoole编译安装
安装完PHP后，即可安装swoole扩展。 swoole扩展下载地址：https://github.com/swoole/swoole-src/releases 尽量选择stable版本，alpha版本最好仅用于实验新特性。 解压源码至任意目录，执行编译安装： 下面采用git下载源码安装：
```
cd /usr/local/src
sudo git clone https://github.com/swoole/swoole-src.git
cd swoole-src
sudo phpize
sudo ./configure
sudo make
sudo make install
```
(注：swoole的./configure有很多额外参数，可以通过./configure --help命令查看，这里选择默认项) 安装完成后，进入/etc/php目录下，打开php.ini文件，在其中加上如下一句：
```
sudo vim /etc/php.ini 添加extension=swoole.so
```
