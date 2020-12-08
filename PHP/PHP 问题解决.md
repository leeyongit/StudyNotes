PHP 问题解决
===
### php56版本部分ajax错误HTTP_RAW_POST_DATA is deprecated头部错误原因

解决方案：

> 打开php.ini配置文件，找到以下代码
> ;always_populate_raw_post_data = -1
> 改成
> always_populate_raw_post_data = -1

