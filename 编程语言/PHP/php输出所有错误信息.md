- 方法一: 修改php.ini

```ini
;显示错误信息
display_errors = On

;显示php开始错误信息
display_startup_errors = On

;日志记录错误信息
log_errors = On
```

- 方法二: 需要调试的php文件首行中加入

```php
ini_set('display_errors',1);            //错误信息
ini_set('display_startup_errors',1);    //php启动错误信息
error_reporting(-1);                    //打印出所有的 错误信息
ini_set('error_log', dirname(__FILE__) . '/error_log.txt'); //将出错信息输出到一个文本文件
```