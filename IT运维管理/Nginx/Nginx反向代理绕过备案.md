# Nginx反向代理绕过备案

在代理服务器上新建一条Nginx server记录，配置如下：

```nginx
server {
    listen 80;
    server_name blog.xxx.net; #设置成需要绑定的域名

location / {
    proxy_pass http://xxx.xxx.xxx.xxx; #指向对应的主机，最好是用IP
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header User-Agent $http_user_agent;
}
```

