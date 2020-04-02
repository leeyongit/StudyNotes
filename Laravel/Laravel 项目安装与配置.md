# Laravel 笔记

### 项目创建
**通过Composer的create-project来安装Laravel**
```php
composer create-project laravel/laravel folder_name
composer create-project laravel/laravel folder_name --prefer-dist "5.8.*"
```
    - --prefer-dist 会从github 上下载.zip压缩包，并缓存到本地。下次再安装就会从本地加载，大大加速安装速度。但她没有保留 .git文件夹,没有版本信息。适合基于这个package进行开发。
    - --prefer-source 会从github 上clone 源代码，不会在本地缓存。但她保留了.git文件夹，从而可以实现版本控制。适合用于修改源代码。

**使用内置web服务器运行项目**
```php
cd folder_name
php -S localhost:8000 -t public/
```

**启动一个Laravel服务**
```sh
php artisan serve
```

**设置Laravel Composer Bin目录路径**
```sh
vi ~/.bash_profile
export PATH=~/.composer/vendor/bin:$PATH
source ~/.bash_profile
```
当你全局安装laravel安装程序时，它会被添加到服务端上的某个位置，因此当你设置路径时，实际上是让你的服务器知道这个目录，因此当你运行命令:`laravel new project`时，它会从composer/bin/目录中找到laravel可执行文件。

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

### 启动调度器
```sh
* * * * * php /home/wwwroot/web.fshd.com/artisan schedule:run >> /dev/null 2>&1
```

### Composer
```sh
composer dump-autoload // 自动加载功能
composer show // 罗列所有扩展包括版本信息
```

### 技巧

**DB:select toArray() 对象转数组的方法**
```php
$resultArray = json_decode(json_encode($result), true);
```

**用DB自带的getQueryLog方法直接打印：**
```php
DB::connection()->enableQueryLog();  // 开启QueryLog
\App\User::find(1);
dump(DB::getQueryLog());
```
