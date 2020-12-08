Yaf安装与配置
---
### 安装
1.下载Yaf包
```php
git clone https://github.com/laruence/yaf.git
```
2. 查找php bin目录
```php
which php
#/Applications/MAMP/bin/php/php7.3.9/bin/php
```
我本机的目录是:/Applications/MAMP/bin/php/php7.2.10/bin/
which这条命令主要是用来查找系统*PATH目录下**的可执行文件。
3.编译

```php
cd ~/Downloads/yaf
 /Applications/MAMP/bin/php/php7.3.9/bin/phpize
 ./configure --with-php-config=/Applications/MAMP/bin/php/php7.3.9/bin/php-config
 make
 make install
 # Installing shared extensions:     /Applications/MAMP/bin/php/php7.3.9/lib/php/extensions/no-debug-non-zts-20170718/
```
初次使用 phpize 需要 安装 autoconf
```php
brew install autoconf
```
4. 查找php配置文件位置
```php
php --ini
#Configuration File (php.ini) Path: /Applications/MAMP/bin/php/php7.2.22/conf
#Loaded Configuration File:         /Applications/MAMP/bin/php/php7.2.22/conf/php.ini
```

### 配置
1.打开php.ini
```php
vim /Applications/MAMP/bin/php/php7.2.22/conf/php.ini
```
2. 在php.ini 中添加以下配置
可以添加在 Module Settings 后面
```php
[yaf]
yaf.environ = product
yaf.library = NULL
yaf.cache_config = 0
yaf.name_suffix = 1
yaf.name_separator = ""
yaf.forward_limit = 5
yaf.use_namespace = 1
yaf.use_spl_autoload = 1
extension="/Applications/MAMP/bin/php/php7.2.22/lib/php/extensions/no-debug-non-zts-20170718/yaf.so"
```
yaf.so 必须配置绝对路径

3. 查看
```php
php -m
```

