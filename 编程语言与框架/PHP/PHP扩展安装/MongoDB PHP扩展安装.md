# MongoDB PHP扩展安装

依照下列步骤 make 一个mongodb.so

```shell
git clone https://github.com/mongodb/mongo-php-driver.git
cd mongo-php-driver
git submodule update --init
phpize
./configure
# 如果使用MAMP
# sudo /Applications/MAMP/bin/php/php7.3.9/bin/phpize
# sudo ./configure --with-php-config=/Applications/MAMP/bin/php/php7.3.9/bin/php-config 
make all
sudo make install
```

寻找php.ini路径

```shell
php -i | grep php.ini
```

并加上

```shell
extension=mongodb.so
```

