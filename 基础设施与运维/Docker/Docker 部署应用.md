# Docker 部署

### Docker 部署Nginx + PHP开发环境
1. 拉取镜像

```sh
docker pull 20.10.1.32:5000/ifensi-app:v0.2
```

2. 生成容器
```sh
docker run -itd -v ~/Desktop/workspace/ifensi-docker/app:/ifensi/App:rw \   # 挂载项目路径
     -v ~/Desktop/workspace/ifensi-docker/vhost:/usr/local/openresty/nginx/conf/vhost \  # 挂载nginx vhost 配置文件
     -p 81:80 \
     --name ifensi \
     20.10.1.32:5000/ifensi-app:v0.2
```

3. Openresty命令
```sh
docker exec ifensi "/usr/bin/openresty"
docker exec ifensi "/usr/bin/openresty" -t
docker exec ifensi "/usr/bin/openresty" -s reload
```
4. 启动php-fpm
```sh
docker exec ifensi "/etc/rc.d/init.d/php-fpm" start
```

### Docker 部署 xunsearch
```sh
docker pull hightman/xunsearch
docker run -d --restart=always --name xunsearch -p 8383:8383 -p 8384:8384 \
    -v /data/docker/xunsearch/data:/usr/local/xunsearch/data hightman/xunsearch:latest
```

### Docker 部署 redis
```sh
 docker run --name fshd-redis -d -p 6379:6379 -v /data/docker/redis/fshd_data:/data redis --requirepass "fshd_@2020_6379"
 docker run --name ad-redis -d -p 6389:6379 -v /data/docker/redis/ad_data:/data redis --requirepass "fshd_@2020_6389"
```
### Docker 部署Mysql

```sh
docker pull mysql:5.6
docker run --name nq-mysql -v /data/mysql:/var/lib/mysql \
    -e MYSQL_ROOT_PASSWORD=ly@dev.2021 -d mysql:5.6
```

### Docker部署ElasticHD

> Elasticsearch 可视化DashBoard, 支持Es监控、实时搜索，Index template快捷替换修改，索引列表信息查看， SQL converts to DSL等

```sh
docker run -p 9200:9200 -d --name elasticsearch elasticsearch
docker run -p 9800:9800 -d --link elasticsearch:demo containerize/elastichd
```

### 如果要挂载目录

```sh
docker run -p 9200:9200 -v /home/esdata:/usr/share/elasticsearch/data -d --name elasticsearch elasticsearch
```

