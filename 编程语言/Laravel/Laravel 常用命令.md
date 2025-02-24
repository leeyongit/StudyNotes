Laravel 常用命令
---
```php
php artisan key:generate
```

### 常用命令

数据迁移 & 数据填充

```sh
php artisan migrate
php artisan migrate --seed
```

清缓存

```sh
php artisan cache:clear
php artisan config:clear
php artisan route:clear # 路由清理缓存
```

### 创建命令
创建模型
```php
php artisan make:model Test (模型文件名)
# 创建模型 分目录的
php artisan make:model Models/Member
```

创建Controller
```php
# 创建控制器
php artisan make:controller IndexController
# 创建资源控制器-生成基本路由方法
php artisan make:controller ResourceController --resource
# 创建资源控制器附带模型
php artisan make:controller TestController --resource --model=Test
```

创建表单验证类
```php
php artisan make:request StoreBlogPost（验证名称）
```

创建数据库迁移
```php
php artisan make:migration create_users_table
```
创建服务提供者
```php
php artisan make:provider TestServiceProvider
```
生成任务类
```php
php artisan make:job TestJob
```
生成命令
```php
php artisan make:command TestCommand
```

### 队列相关
```php
php artisan queue:restart # 重启队列
php artisan queue:failed-table # 处理失败的任务
php artisan queue:work redis --sleep=3 --tries=3
php artisan queue:work --daemon --quiet --queue=default --delay=3 --sleep=3 --tries=3
```

