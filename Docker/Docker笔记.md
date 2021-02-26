# Docker 笔记

## Docker 安装 (CentOS 7)
```sh
yum -y install docker-io

# 使用官方安装脚本自动安装 （仅适用于公网环境）
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun
```
基于CentOS 7构建，安装Docker环境参考官网。国内环境，推荐使用[阿里云Docker Hub加速器服务。
```sh
$ sudo mkdir -p /etc/docker
$ sudo tee /etc/docker/daemon.json <<-'EOF'
{
  "registry-mirrors": ["https://muehonsf.mirror.aliyuncs.com"]
}
EOF
```

## 常用命令

```sh
sudo systemctl daemon-reload # 重新加载配置文件
sudo systemctl restart docker # 重启相关服务
sudo systemctl enable docker.service # 开机自启动
sudo systemctl status docker.service # 查看运行状态 Loaded行：配置文件的位置，是否设为开机启动
```
上面的命令相当于在/etc/systemd/system目录添加一个符号链接，指向/usr/lib/systemd/system里面的docker.service文件。
这是因为开机时，Systemd只执行/etc/systemd/system目录里面的配置文件。这也意味着，如果把修改后的配置文件放在该目录，就可以达到覆盖原始配置的效果。

## 安装Docker Compose

```sh
$ curl -L https://github.com/docker/compose/releases/download/1.25.0/docker-compose-`uname -s`-`uname -m` > ./docker-compose
$ sudo mv ./docker-compose /usr/bin/docker-compose
$ sudo chmod +x /usr/bin/docker-compose
# 国内安装
curl -L https://get.daocloud.io/docker/compose/releases/download/1.23.2/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

## 安装 ctop 工具可以帮助查看容器在主机的使用情况。

```sh
sudo wget https://github.com/bcicen/ctop/releases/download/v0.7.1/ctop-0.7.1-linux-amd64 -O /usr/local/bin/ctop
sudo chmod +x /usr/local/bin/ctop
```

## 常用Docker命令

```sh
docker attach containerID # 连接到正在运行中的容器
docker exec -it containerID /bin/bash # 使用docker exec进入Docker容器
docker exec -it containerID /bin/sh (alpine)

sudo docker cp host_path containerID:container_path # 从主机复制到容器
sudo docker cp containerID:container_path host_path # 从容器复制到主机

docker tag ImageId REPOSITORY:TAG（仓库：标签） # 给镜像打标签

docker save ImageId > web.tar # 保存镜像
docker load -i web.tar    # 导入镜像 --input , -i : 指定导入的文件
docker commit -a="作者"  -m="提交的描述信息"  容器id   要创建的目标镜像名:[标签名]
docker push NAME[:TAG] # 将本地的镜像上传到镜像仓库,要先登陆到镜像仓库
```

退出容器
```sh
ctrl+d 退出容器且关闭, docker ps 查看无
ctrl+p+q 退出容器但不关闭, docker ps 查看有
```

全部停止／删除容器／删除镜像
```sh
docker stop $(docker ps -a -q);
docker rm $(docker ps -a -q);
docker rmi -f $(docker images -a -q)
```

清理docker volumes
```sh
docker system df
docker system prune
```


