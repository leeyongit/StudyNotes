Laravel 常用命令
---
## 常用命令
### 数据迁移 & 数据填充
```php
php artisan migrate
php artisan migrate --seed
```

### 清缓存
```php
php artisan cache:clear
php artisan config:clear
```

### 创建Controller
```php
php artisan make:controller IndexController 创建控制器
php artisan make:controller ResourceController --resource  生成基本路由方法
```
### 创建Model
```php
php artisan make:model 模型文件名
php artisan make:model Models/Member 创建模型 分目录的
```
```
php artisan make:provider TestServiceProvider 创建服务提供者
php artisan make:job SendReminderEmail 生成任务类
php artisan make:command SendEmails 生成命令
```


### 队列相关
```php
php artisan queue:restart # 重启队列
php artisan queue:failed-table # 处理失败的任务

php artisan queue:work redis --sleep=3 --tries=3
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```
