
### 1. php的生命周期

PHP 生命周期关键词

1. MINIT（module init 模块初始化）

- 启动 PHP 输出、初始化垃圾回收器
- 启动 Zend 引擎、注册 Zend 核心扩展、Zend 标准常量
- 解析 PHP.ini，映射 PHP.ini 相关配置
- 注册静态、动态编译的扩展
- 回调各扩展定义的 module starup 钩子函数

用一句话来说，就是初始化类、常量、扩展等 PHP 所用到的资源

2. RINIT（request init 请求初始化）

PHP 初始化脚本执行的基本环境，SAPI 将控制权交给 PHP，激活 Zend 引擎，初始化执行器

3. PHP 脚本执行

Zend 引擎接管控制权，将 PHP 脚本编译成 Opcodes，并顺序执行

4. RSHUTDOWN (request shutdown 请求结束)

PHP 脚本执行完之后进入请求结束阶段，PHP 启动清理程序，这个阶段，将 flush 输出内容，发送 http 响应内容，关闭 PHP 执行器

5. MSHUTDOWN(module shutdown 模块关闭)

这个阶段主要是进行资源的清理、php 各模块的关闭操作，同时，将回调各扩展的 module shutdown 钩子函数

### 2. php的垃圾回收机制(zval)
[PHP垃圾回收机制](https://www.jianshu.com/p/e39ce8aafe52)

### php-fpm的两种通信模式tcp socket 和 unix socket


### 讲述php-fpm的运行原理
php-fpm是一种master（主）/worker（子）多进程架构。master进程主要负责CGI及PHP环境初始化、事件监听、子进程状态等等，worker进程负责处理php请求。
从FPM接收到请求，到处理完毕，其具体的流程如下：
（1）FPM的master进程接收到请求。
（2）master进程根据配置指派特定的worker进程进行请求处理，如果没有可用进程，返回错误，这也是我们配合Nginx遇到502错误比较多的原因。
（3）worker进程处理请求，如果超时，返回504错误。
（4）请求处理结束，返回结果。