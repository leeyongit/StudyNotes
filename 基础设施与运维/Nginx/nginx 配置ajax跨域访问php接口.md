# Nginx 配置ajax跨域访问php接口

> 配置nginx.conf文件

在nginx.conf里面,找到server项,并在里面添加如下配置

```nginx
location ~ \.php?($|/)
{
    #try_files $uri =404;
    #handel cosr    by mao
    add_header 'Access-Control-Allow-Origin' '*';
    add_header 'Access-Control-Allow-Credentials' 'true';
    add_header 'Access-Control-Allow-Methods' 'OPTION, POST, GET';
    add_header 'Access-Control-Allow-Headers' 'X-Requested-With, Content-Type';
    fastcgi_pass  unix:/tmp/php-cgi.sock;
    fastcgi_index index.php;
    include fastcgi.conf;
}
```

重启服务器,完成

