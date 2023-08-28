# 现代化PHP
* 命名空间
* PSR规范
* Composer包管理器
* 匿名和闭包函数
* Traits
* 新的语法
* PHP 7 性能

## PSR
PSR 是 PHP Standard Recommendations （PHP 推荐标准）的简写，由 PHP FIG 组织制定的 PHP 规范，是 PHP 开发的实践标准。
- PSR-1 基础编码规范
- PSR-2 编码风格规范
- PSR-3 日志接口规范
- PSR-4 自动加载规范

## 依赖注入实现原理
依赖注入是实现程序解耦的一种方式。是用于实现控制反转（Inversion of Control）的最常见的方式之一。

## Autoload、Composer 原理
composer加载核心思想是通过composer的配置文件在引用入口文件(autoload.php)时,将类和路径的对应关系加载到内存中,最后将具体加载的实现注册到spl_autoload_register函数中.最后将需要的文件包含进来.

## yield
生成器语法
一个生成器函数看起来像一个普通的函数，不同的是普通函数返回一个值，而一个生成器可以yield生成许多它所需要的值。
**yield关键字**
生成器函数的核心是yield关键字。它最简单的调用形式看起来像一个return申明，不同之处在于普通return会返回值并终止函数的执行，而yield会返回一个值给循环调用此生成器的代码并且只是暂停执行生成器函数。


