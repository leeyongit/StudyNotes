Zip扩展安装
---
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
