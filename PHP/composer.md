# composer 设置

**Composer 安装**

```sh
curl -sS https://getcomposer.org/installer | php && mv composer.phar /usr/local/bin/composer
```

**Composer 中文镜像**

```sh
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

**修改 composer 超时时间**

```sh
composer config --list // 可以看到默认是多长时间
composer config -g process-timeout 600
```


**安装composer包**

```
composer require firebase/php-jwt
composer require elasticsearch/elasticsearch
```

### Composer
```sh
composer dump-autoload // 自动加载功能
composer show // 罗列所有扩展包括版本信息
composer update --prefer-source # Install of kylekatarnls/update-helper failed
```

## 创建一个自己的 Composer 库
1. 准备工作
GitHub 账号 相信每一位程序员都有。
[Packagist 账号](https://packagist.org/) 这个你就不一定有了。（可以使用 GitHub 账户授权登录）
2. 创建自己的项目
可以先在 GitHub 上创建一个仓库，然后克隆到本地，接下来就是创建自己的 Project。在项目跟目录添加一个 composer.json 文件，用来描述项目信息，提交到 Packagist 会检测这个文件。文件内容如下：
```json
{
    "name": "your-vendor-name/package-name",
    "description": "A short description of what your package does",
    "type": "project",
    "license": "MIT",
    "minimum-stability": "stable",
    "require": {}
}
```

