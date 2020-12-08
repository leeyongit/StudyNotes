#Mac 上MAMP Pro 不支持pathinfo 解决方案

在mac上配置url 重写 ，使用MAMP PRO 配置 完url 重写后，发现不支持 pathinfo 的解决办法

```ini
location / {
   if (!-e $request_filename) {
    rewrite  ^(.*)$  /index.php?s=/$1  last;
    break;
    }
 }


location ~^(.+\.php)(.*)$ {
   
    fastcgi_pass    unix:/Applications/MAMP/Library/logs/fastcgi/nginxFastCGI_php7.2.22.sock;
    fastcgi_index  index.php;

    include  /Applications/MAMP/conf/nginx/fastcgi_params;
    fastcgi_split_path_info ^(.+\.php)(\/?.*)$;
    fastcgi_param PATH_INFO $fastcgi_path_info;


    #强制将某些非法地址交给index.php处理
    set $new_fastcgi_script_name $fastcgi_script_name;
    if (!-e $document_root$fastcgi_script_name) {
            set $new_fastcgi_script_name "/index.php";
    }
    fastcgi_param    SCRIPT_FILENAME $document_root$new_fastcgi_script_name;
    fastcgi_param    SCRIPT_NAME $new_fastcgi_script_name ;
}


```

