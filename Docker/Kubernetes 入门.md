Kubernetes 入门
---
Master（主节点）： 控制 Kubernetes 节点的机器，也是创建作业任务的地方。
Node（节点）： 这些机器在 Kubernetes 主节点的控制下执行被分配的任务。
Pod： 由一个或多个容器构成的集合，作为一个整体被部署到一个单一节点。同一个 pod 中的容器共享 IP 地址、进程间通讯（IPC）、主机名以及其它资源。Pod 将底层容器的网络和存储抽象出来，使得集群内的容器迁移更为便捷。
Replication controller（复制控制器）： 控制一个 pod 在集群上运行的实例数量。
Service（服务）： 将服务内容与具体的 pod 分离。Kubernetes 服务代理负责自动将服务请求分发到正确的 pod 处，不管 pod 移动到集群中的什么位置，甚至可以被替换掉。
Kubelet： 这个守护进程运行在各个工作节点上，负责获取容器列表，保证被声明的容器已经启动并且正常运行。
kubectl： 这是 Kubernetes 的命令行配置工具。

####环境初始化

初始化所需要执行的命令，都是根据在安装过程中遇到的报错，给出的解决办法整理。

```
# 安装docker服务
sudo yum install docker

# 启用docker服务
sudo systemctl enable docker.service

# 关闭防火墙
sudo systemctl stop firewalld

# 关闭内存交换
sudo swapoff -a

# 修改文件驱动
sudo vi /lib/systemd/system/docker.service
# 修改为cgroupfs
--exec-opt native.cgroupdriver=cgroupfs
# 重新加载配置
systemctl daemon-reload
# 重启docker
systemctl restart docker

# 关闭selinux
sudo setenforce 0
```



#### 安装Minikube

Mac

```sh
brew install minikube
```

Linux

```shell
# 也可以下载最新版
> curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/

> minikube -h
```

#### 安装Kubectl

```shell
> curl -Lo kubectl https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl && chmod +x kubectl && sudo mv kubectl /usr/local/bin/

> kubectl -h
```

#### 启动minikube

```sh
minikube start --vm-driver=none --registry-mirror=https://registry.docker-cn.com
```



## k8s 部署问题解决

1. WARNING IsDockerSystemdCheck

 修改或创建`/etc/docker/daemon.json`，加入下述内容：



```json
{
  "exec-opts": ["native.cgroupdriver=systemd"]
}
```

重启`docker`：

```undefined
systemctl restart docker
```

查看修改后的状态：

```undefined
docker info | grep Cgroup
```

2. WARNING FileExisting-socat

```sh
yum install socat 
```

3. Kubernetes 1.8开始要求关闭系统的Swap，如果不关闭，默认配置下kubelet将无法启动，关闭系统的Swap方法如下:

```sh
  swapoff -a
```

