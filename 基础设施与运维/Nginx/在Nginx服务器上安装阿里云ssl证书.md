# 在Nginx服务器上安装阿里云ssl证书

阿里云SSL证书服务支持下载证书并安装到Nginx、Tengine服务器上。

###  配置nginx

打开Nginx安装目录/conf/nginx.conf配置文件并进行编辑

```nginx
server {
    listen 443;
    server_name your-domain.com; 									// 你的域名
    ssl on;
    root /var/www/html; 													// 前台文件存放文件夹，可改成别的
    index index.html index.htm;										// 上面配置的文件夹里面的index.html
    
    ssl_certificate  cert/214292799730473.pem;		// 改成你的证书的名字
    ssl_certificate_key cert/214292799730473.key;	// 你的证书的名字
    
    ssl_session_timeout 5m;
    ssl_ciphers ECDHE-RSA-AES128-GCM-SHA256:ECDHE:ECDH:AES:HIGH:!NULL:!aNULL:!MD5:!ADH:!RC4;
    ssl_protocols TLSv1 TLSv1.1 TLSv1.2;
    ssl_prefer_server_ciphers on;
  
    location / {
        index index.html index.htm;
    }
}
server {
    listen 80;
    server_name your-domain.com;// 你的域名
    rewrite ^(.*)$ https://$host$1 permanent;			// 把http的域名请求转成https
}
```

配置完成后，检查一下 nginx 配置文件是否可用，有 successful 表示可用。

```sh
$ nginx -t # 检查nginx配置文件
```

配置正确后，重新加载配置文件使配置生效：

```sh
$ nginx -s reload # 使配置生效
```

