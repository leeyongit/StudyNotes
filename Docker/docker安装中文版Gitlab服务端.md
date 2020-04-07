Docker安装中文版Gitlab服务端
---
1.pull中文版镜像：
```sh
docker pull beginor/gitlab-ce:11.3.0-ce.0
```
2.创建目录：
    通常会将 GitLab 的配置 (etc) 、 日志 (log) 、数据 (data) 放到容器之外， 便于日后升级， 因此请先准备这三个目录。

```sh
sudo mkdir -p /home/docker/gitlab/etc
sudo mkdir -p /home/docker/gitlab/log
sudo mkdir -p /home/docker/gitlab/data
```
3.修改目录权限：
```sh
chmod 777 /home/docker/gitlab/etc
chmod 777 /home/docker/gitlab/log
chmod 777 /home/docker/gitlab/data
```
4.运行：
    准备好这三个目录之后， 就可以开始运行 Docker 镜像了：
```sh
docker run \
    --detach \
    --publish 8443:443 \
    --publish 8080:80 \
    --publish 8222:22 \
    --name gitlab \
    --restart unless-stopped \
    --volume /home/docker/gitlab/etc:/etc/gitlab \
    --volume /home/docker/gitlab/log:/var/log/gitlab \
    --volume /home/docker/gitlab/data:/var/opt/gitlab \
    --privileged=true \
    beginor/gitlab-ce:11.3.0-ce.0
```