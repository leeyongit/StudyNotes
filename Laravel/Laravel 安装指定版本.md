# Laravel 安装指定版本

1，进入根目录（本地的www目录）安装指定版本， demo为项目的文件夹名称，这里安装的是5.5的版本；

```
composer create-project laravel/laravel=6.x demo --prefer-dist
```

2，如果报错说超出缓存字节大小限制或者超时，不妨更换阿里云的 **`composer`** 源（亲测可用）

```
composer config repo.packagist composer https://mirrors.aliyun.com/composer/
```

 `repo` 是 `repositories` 的简写方式

*上面的命令值针对当前的项目有效，如果需要设置全局的在config后面加 `-g` 或 `--global`*

> 注意：
>    如果是只是因为超过缓存的大小，那只需要将缓存大小修改一下就好了
>    修改命令：`composer config cache-files-maxsize 800MiB`

3，查看Laravel 当前版本号；

```sh
php artisan --version
```



## 问题解决

> 解决composer install遇到：Your requirements could not be resolved to an installable set of packages

解决办法：直接忽略版本就是了

composer install --ignore-platform-reqs 或者 composer update --ignore-platform-reqs