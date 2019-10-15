# 使用Docker安装Gitlab 

### Install the GitLab EE Image

```sh
sudo docker pull gitlab/gitlab-ee:latest
```

### Configure and Run GitLab

在上面的命令中，将--hostname选项的值和external_url配置设置的值替换为GitLab站点的域或子域。如果您没有为您的站点设置DNS，请为external_url设置输入http：// your_linode_ip（而不是https）。然后，运行该命令

```sh
sudo docker run --detach \
  --hostname gitlab.fshd.com \
  --publish 1443:443 --publish 180:80 --publish 122:22 \
  --name gitlab-fshd \
  --restart always \
  --volume /volume1/gitlab/config:/etc/gitlab \
  --volume /volume1/gitlab/logs:/var/log/gitlab \
  --volume /volume1/gitlab/data:/var/opt/gitlab \
  --env GITLAB_OMNIBUS_CONFIG="external_url 'http://192.168.0.116';" \
  gitlab/gitlab-ee:latest
```

在容器启动后，还需要几分钟才能在浏览器中访问GitLab。您可以通过监视日志找到有关启动过程的更多信息：

```sh
sudo docker logs -f gitlab-fshd
```



```bash
docker run -d \
    --hostname gitlab.fshd.com \
    -p 8080:80 \
    -p 4433:443 \
    -p 2222:22 \
    --name gitlab \
    --restart unless-stopped \
    -v gitlab-config:/etc/gitlab \
    -v gitlab-logs:/var/log/gitlab \
    -v gitlab-data:/var/opt/gitlab \
    --network gitlab-net \
    twang2218/gitlab-ce-zh:11.1.4
```