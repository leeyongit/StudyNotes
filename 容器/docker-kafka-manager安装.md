docker——kafka-manager安装
========================

1. 下载kafka-manager镜像：
> docker pull sheepkiller/kafka-manager

2. 编写docker-compose.yml文件
```sh
version: '2'
services:
  kafka-manager:
    image: sheepkiller/kafka-manager            # 如果要安装web管理工具可以同时安装这个，最后通过苏主机IP的9000端口进行访问，例如172.31.148.174:9000
    environment:
      ZK_HOSTS: 192.168.0.66:62181,192.168.0.66:62182,192.168.0.66:62183
      APPLICATION_SECRET: "letmein"
    ports:
      - "39000:9000"
    expose:
      - "9000"
```
