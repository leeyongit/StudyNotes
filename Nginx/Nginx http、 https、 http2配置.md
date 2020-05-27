nginx http、 https、 http2配置
---
nginx版本：1.12.2
```sh
 # 首先找到nginx.conf
 # 查看nginx.conf 路径
 nginx -t
```
*注：以下配置重启才能生效

### http配置

1、配置域名
```nginx
// 比如配置成baidu.com
http {
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  baidu.com;
    }
}
```
2、域名的访问地址
```sh
http {
    server {
        listen       80 default_server;
        listen       [::]:80 default_server;
        server_name  baidu.com;                 //  域名
        root         /server/www/               //  访问baidu.com 所访问的服务器路径
    }
}
```

### http2 & https
配置域名和访问的路径与http完全相同，区别在于证书
查看443端口是否打开
1.找个个证书

2.修改nginx.conf 文件
```sh
  server {
         listen       443 ssl http2 default_server;
         listen       [::]:443 ssl http2 default_server;
         server_name  m.xxx.com;
         root         /service;

         ssl_certificate "/etc/nginx/cert/214744744310771.pem";         //  证书目录
         ssl_certificate_key "/etc/nginx/cert/214744744310771.key";     //  证明目录
         ssl_session_cache shared:SSL:1m;
         ssl_session_timeout  5m;
         ssl_ciphers HIGH:!aNULL:!MD5;
         ssl_prefer_server_ciphers on;

         # Load configuration files for the default server block.
         include /etc/nginx/default.d/*.conf;

         location / {
         }

         error_page 404 /404.html;
             location = /40x.html {
         }

         error_page 500 502 503 504 /50x.html;
             location = /50x.html {
         }
     }
```