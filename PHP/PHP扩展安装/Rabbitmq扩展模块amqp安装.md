# Rabbitmq 扩展模块amqp安装
## 1. 安装rabbitmq-c-0.7.1
```bash
cd /usr/local
wget https://github.com/alanxz/rabbitmq-c/releases/download/v0.7.1/rabbitmq-c-0.7.1.tar.gz
tar -xzvf   rabbitmq-c-0.7.1.tar.gz
cd rabbitmq-c-0.7.1
./configure --prefix=/usr/local/rabbitmq-c-0.7.1
make && make install
```
生产.so文件位置
```bash
/usr/local/rabbitmq-c-0.7.1/lib/librabbitmq.so
```

## 2. 第二步安装amqp

运行命令：pecl install amqp 运行该命令会出现如下提示，

> Set the path to librabbitmq install prefix [autodetect] :
需要将安装rabbitmq-c-0.7.1的目录输入进去 /usr/local/rabbitmq-c-0.7.1 然后按回车。

> You should add "extension=amqp.so" to php.ini

```sh
pecl config-set "/Applications/MAMP/bin/php/php7.3.9/conf/php.ini"
```

## 安装php rabbit 扩展包
```php
composer require bschmitt/laravel-amqp
```
https://packagist.org/packages/bschmitt/laravel-amqp
https://github.com/bschmitt/laravel-amqp
```php
// 生产
\Bschmitt\Amqp\Facades\Amqp::publish('routing-key', 'message1111222' , ['queue' => 'queue-name']);

// 消费
\Bschmitt\Amqp\Facades\Amqp::consume('queue-name', function ($message, $resolver) {

      var_dump($message->body);

      $resolver->acknowledge($message);

      $resolver->stopWhenProcessed();

  });
```