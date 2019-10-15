# Laravel 笔记

**通过Composer的create-project来安装Laravel**

- --prefer-dist 会从github 上下载.zip压缩包，并缓存到本地。下次再安装就会从本地加载，大大加速安装速度。但她没有保留 .git文件夹,没有版本信息。适合基于这个package进行开发。
- --prefer-source 会从github 上clone 源代码，不会在本地缓存。但她保留了.git文件夹，从而可以实现版本控制。适合用于修改源代码。


**快速创建项目**
```sh
composer create-project --prefer-dist laravel/laravel blog
composer create-project --prefer-dist laravel/laravel blog 5.6.*
cd blog
php -S localhost:8000 -t public/
```

**Composer 环境变量**
```sh
vi ~/.bash_profile
export PATH=~/.composer/vendor/bin:$PATH
source ~/.bash_profile
```

### Laravel 的远程服务器任务处理器 Envoy
```sh
envoy run deploy
```

### nginx laravel 配置
```sh
location / {
    try_files $uri $uri/ /index.php?$query_string;
}
```

## 常用命令
### 数据迁移 & 数据填充
```sh
php artisan migrate
php artisan migrate --seed
```

### 清缓存
```sh
php artisan cache:clear
php artisan config:clear
```

### 生成文件
php artisan make:controller IndexController 创建控制器
php artisan make:controller ResourceController --resource  生成基本路由方法
---
php artisan make:model 模型文件名
php artisan make:model Models/Member 创建模型 分目录的
---
php artisan make:provider TestServiceProvider 创建服务提供者
php artisan make:job SendReminderEmail 生成任务类
php artisan make:command SendEmails 生成命令

### 队列相关
```sh
php artisan queue:restart # 重启队列
php artisan queue:failed-table # 处理失败的任务

php artisan queue:work redis --sleep=3 --tries=3
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```



### Composer
```sh
composer create-project laravel/laravel folder_name
composer create-project laravel/laravel folder_name --prefer-dist "5.8.*"
composer dump-autoload // 自动加载功能
composer show // 罗列所有扩展包括版本信息

```

### 启动调度器
```sh
* * * * * php /home/wwwroot/web.fshd.com/artisan schedule:run >> /dev/null 2>&1
```

### 启动内置服务器
```sh
php artisan serve
```

DB:select toArray() 对象转数组的方法
$resultArray = json_decode(json_encode($result), true);
