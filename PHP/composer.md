# Setting composer

## Composer 安装：
curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer

## Composer 中文镜像
composer config -g repo.packagist composer https://packagist.laravel-china.org 

## 修改 composer 超时时间
composer config --list // 可以看到默认是多长时间
composer config -g process-timeout 600
