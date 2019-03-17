# Laravel 笔记

## 通过Composer的create-project来安装Laravel
```sh
composer create-project laravel/laravel itapp --prefer-dist
cd itapp
php -S localhost:8000 -t public/
```

## Laravel安装设置~/.composer/vendor/bin 路径加到 PATH
```sh
export PATH=~/.composer/vendor/bin:$PATH
```

## 设置PATH
```sh
vi ~/.bash_profile
source ~/.bash_profile
```

## Laravel 的远程服务器任务处理器 Envoy
```sh
envoy run deploy
root 1qazxsw234
```

## nginx laravel 配置
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

### 生成控制器
```sh
php artisan make:controller IndexController
php artisan make:controller ResourceController --resource // 生成基本路由方法
```

### 创建模型
```sh
php artisan make:model 模型文件名
php artisan make:model Models/Member # 分目录的
```

### 创建服务提供者
```sh
php artisan make:provider TestServiceProvider
```

### 生成任务类
```sh
php artisan make:job SendReminderEmail
```

### 队列相关
```sh
php artisan queue:restart # 重启队列
php artisan queue:failed-table # 处理失败的任务

php artisan queue:work redis --sleep=3 --tries=3
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```

### 生成命令
```sh
php artisan make:command SendEmails
```

### 自动加载功能
```sh
composer dump-autoload
composer dumpautoload
```

### 启动调度器
```sh
* * * * * php /path-to-your-project/artisan schedule:run >> /dev/null 2>&1
```

### 启动内置服务器
```sh
php artisan serve
```

