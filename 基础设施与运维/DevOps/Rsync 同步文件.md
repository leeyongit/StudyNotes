# rsync 同步文件

服务器同步电影到客户机

## 服务端

服务端修改配置文件

```sh
vim /etc/rsyncd.conf
```
服务端修改配置文件 /etc/rsyncd.conf

```yaml
[share_video]
path = /volume1/fshd/nas/video
use chroot = no
max connections = 15
read only = yes
write only = no
list = no
ignore errors = yes
timeout = 120
```

启动服务
```sh
rm /var/run/rsyncd.pid
/usr/bin/rsync --daemon
```

## 客户端

```sh
rsync -avz --progress root@192.168.0.117:/volume1/fshd/nas/video/  /volume1/fshd/nas/video/
```



