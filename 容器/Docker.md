## Docker 部署 (CentOS 7)
yum -y install docker-io

## 安装Docker与Docker Compose
基于CentOS 7构建，安装Docker环境参考官网。国内环境，推荐使用[阿里云Docker Hub加速器服务。
```sh
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://muehonsf.mirror.aliyuncs.com"]
}
EOF
```
## 重新加载配置文件
```sh
sudo systemctl daemon-reload
```
## 重启相关服务
```sh
sudo systemctl restart docker
```
## 开机自启动
```sh
sudo systemctl enable docker.service
```
## 查看运行状态 Loaded行：配置文件的位置，是否设为开机启动
```sh
sudo systemctl status docker.service 
```

上面的命令相当于在/etc/systemd/system目录添加一个符号链接，指向/usr/lib/systemd/system里面的docker.service文件。
这是因为开机时，Systemd只执行/etc/systemd/system目录里面的配置文件。这也意味着，如果把修改后的配置文件放在该目录，就可以达到覆盖原始配置的效果。

## 安装Docker Compose。
```sh
$ curl -L https://github.com/docker/compose/releases/download/1.16.1/docker-compose-`uname -s`-`uname -m` > ./docker-compose
$ sudo mv ./docker-compose /usr/bin/docker-compose
$ sudo chmod +x /usr/bin/docker-compose
```

## 安装 ctop 工具可以帮助查看容器在主机的使用情况。
```sh
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.1/ctop-0.7.1-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop
```

## docker 进入容器
```sh
docker exec -it containerID /bin/bash 
docker exec -it containerID /bin/sh (alpine)
```

## docker 打包和导入
```sh
docker save ImageId > /home/web.tar
docker load --input /home/web.tar
```
## 从主机复制到容器
```sh
sudo docker cp host_path containerID:container_path
```
## 从容器复制到主机
```sh
sudo docker cp containerID:container_path host_path
```

## 退出容器
ctrl+d 退出容器且关闭, docker ps 查看无
ctrl+p+q 退出容器但不关闭, docker ps 查看有

## 全部停止／删除容器／删除镜像
```sh
docker stop $(docker ps -a -q);
docker rm $(docker ps -a -q);
docker rmi -f $(docker images -a -q)
```

## 清理docker volumes
```sh
docker system df
docker system prune
```

## Docker 部署 xunsearch
```sh
docker pull hightman/xunsearch
docker run -d --restart=always --name xunsearch -p 8383:8383 -p 8384:8384 -v /home/docer/xunsearch/data:/usr/local/xunsearch/data hightman/xunsearch:latest
```

## Docker 部署 syncthing 中继服务器
```sh
docker run -d --restart=always -p 22067:22067 -p 22070:22070 --name=fshd_relay kylemanna/syncthing-relay
# relay://39.107.158.137:22067/?id=O7LVRER-IFY2CR6-ZUHZBGD-TUYTT7P-2BK5JPC-AVLLTVE-UTBAFZX-O3VBFQY&pingInterval=1m0s&networkTimeout=2m0s&sessionLimitBps=0&globalLimitBps=0&statusAddr=:22070&providedBy=
```


