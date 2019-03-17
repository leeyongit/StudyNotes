
## 安装swoole
```sh
#!/bin/bash
pecl install swoole
```
## -bash: pecl: command not found的解决方法
出现这个问题是因为没有安装php的扩展开发导致的，建议将php以及apache的开发都安装上，使用如下的命令。
```sh
yum install php-devel php-pear httpd-devel
yum install openssl
yum install openssl-devel
```