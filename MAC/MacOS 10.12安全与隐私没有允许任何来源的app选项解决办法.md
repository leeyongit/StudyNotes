MacOS 10.12安全与隐私没有允许任何来源的app选项解决办法
===============================================

不少用户更新到macOS10.12之后 发现在系统偏好设置->安全性与隐私中已经去除了允许安装任何来源App的选项，没有这个选项估计好多不是从Mac App Store中下载的软件都不能正常安装，官方给出的解决办法是 control + 点按要打开的App文件即可，但是感觉还是不方便，查询了官方的支持Gatekeeper的原因，10.12默认是打开的，只需要关掉Gatekeeper就可以看到选项了。
具体执行方法是在终端执行一下方法
显示允许安装任何来源的App
sudo spctl --master-disable

隐藏允许安装任何来源的App
sudo spctl --master-enable


log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for" $request_time';

/usr/local/etc/goaccess/goaccess.conf 
## 修改配置文件（在配置文件的末尾处添加上）：
time-format %H:%M:%S
date-format %d/%b/%Y
log-format %h %^[%d:%t %^] "%r" %s %b "%R" "%u"
其中，log-format 与 access.log 的 log_format 格式对应，每个参数以空格或者制表符分割。参数说明如下：
%t  匹配time-format格式的时间字段
%d  匹配date-format格式的日期字段
%h  host(客户端ip地址，包括ipv4和ipv6)
%r  来自客户端的请求行
%m  请求的方法
%U  URL路径
%H  请求协议
%s  服务器响应的状态码
%b  服务器返回的内容大小
%R  HTTP请求头的referer字段
%u  用户代理的HTTP请求报头
%D  请求所花费的时间，单位微秒
%T  请求所花费的时间，单位秒
%^  忽略这一字段
