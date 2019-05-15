docker 升级到最新版
=================
步骤
卸载旧版 docker
```
~ rpm -qa | grep docker # 也可以使用 yum list installed | grep docker
docker-engine-1.13.0-1.el7.centos.x86_64
docker-engine-selinux-1.13.0-1.el7.centos.noarch
~ yum remove docker-engine-1.13.0-1.el7.centos.x86_64
~yum remove docker-engine-selinux-1.13.0-1.el7.centos.noarch
```
升级至最新版
```
> curl -fsSL https://get.docker.com/ | sh
```
重启docker
```
> systemctl restart docker # centos 7
> service docker restart # centos 6
```
查看升级后的版本
```
> docker version # docker -v
```
将当前用户加入docker用户组
sudo usermod -a -G dockerroot $USER
sudo usermod -a -G dockerroot dockerroot