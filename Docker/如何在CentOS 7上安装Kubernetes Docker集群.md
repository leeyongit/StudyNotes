如何在CentOS 7上安装Kubernetes Docker集群
---
Kubernetes是一个开放源代码平台，用于管理Google开发的容器化应用程序。它允许您在集群环境中管理，扩展和自动部署容器化的应用程序。借助Kubernetes，我们可以跨多个主机协调我们的容器，使用所有资源即时扩展容器化的应用程序，并拥有集中的容器管理环境。

在本教程中，我将逐步向您展示如何在CentOS 7上安装和配置Kubernetes。我们将使用1台服务器“ k8s-master”作为Kubernetes Host Master，使用2台服务器作为Kubernetes节点"node01"和" node02"。

## d先决条件
3个CentOS 7服务器
10.0.15.10 k8s-master
10.0.15.21 node01
10.0.15.22 node02
根特权
## 我们要做什么？
Kubernetes安装
Kubernetes集群初始化
将node01和node02添加到集群
测试-创建第一个Pod
## 第1步-Kubernetes安装
在第一步中，我们将为Kubernetes安装准备这三台服务器，因此在主服务器和节点服务器上运行所有命令。
我们将通过更改服务器上的现有配置来准备所有服务器以进行Kubernetes的安装，并安装一些软件包，包括docker-ce和kubernetes本身。

#### 配置主机
使用vim编辑器在所有服务器上编辑主机文件  。
```sh
vim /etc/hosts
```
将以下列表粘贴到hosts里面。
```sh
106.75.7.152 k8s-master
123.56.112.76 node01
10.0.15.22 node02
```
保存并退出。
#### 禁用SELinux
在本教程中，我们不会介绍Docker的SELinux配置，因此我们将其禁用。

运行以下命令以禁用SELinux。
```sh
setenforce 0
sed -i --follow-symlinks 's/SELINUX=enforcing/SELINUX=disabled/g' /etc/sysconfig/selinux
```
#### 启用br_netfilter内核模块
kubernetes安装需要br_netfilter模块。启用此内核模块，以便iptables处理通过网桥的数据包​​以进行过滤和端口转发，并且群集中的kubernetes pod可以相互通信。

运行以下命令以启用br_netfilter内核模块。
```sh
modprobe br_netfilter
echo '1' > /proc/sys/net/bridge/bridge-nf-call-iptables
```

#### 禁用交换
通过运行以下命令来禁用SWAP的kubernetes安装。
```sh
swapoff -a
```
![禁用交换](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/1.png)

然后编辑"/etc/fstab"文件。

```sh
vim /etc/fstab
```
注释掉交换线UUID如下。

![编辑/ etc / fstab](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/1b.png)


#### 安装Docker CE
从Docker存储库安装最新版本的Docker-ce。

安装docker-ce的软件包依赖关系。
```sh
yum install -y yum-utils device-mapper-persistent-data lvm2
```
将docker存储库添加到系统并使用yum命令安装docker-ce。
```sh
yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo
yum install -y docker-ce
```

#### 安装Kubernetes
通过运行以下命令将kubernetes存储库添加到centos 7系统。
```sh
cat <<EOF > /etc/yum.repos.d/kubernetes.repo
[kubernetes]
name=Kubernetes
baseurl=http://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=http://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg
        http://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF
```
现在，使用下面的yum命令安装kubernetes软件包kubeadm，kubelet和kubectl。
```sh
yum install -y kubelet kubeadm kubectl
```
安装完成后，重新启动所有这些服务器。
```sh
sudo reboot
```

再次登录到服务器并启动服务docker和kubelet。
```sh
systemctl start docker && systemctl enable docker
systemctl start kubelet && systemctl enable kubelet
```

#### 更改cgroup驱动程序
我们需要确保docker-ce和kubernetes使用相同的'cgroup'。

使用docker info命令检查docker cgroup。
```sh
docker info | grep -i cgroup
```
您会看到Docker使用'cgroupfs'作为cgroup驱动程序。

现在运行以下命令，将kuberetes cgroup-driver更改为"cgroupfs"。
```sh
sed -i 's/cgroup-driver=systemd/cgroup-driver=cgroupfs/g' /usr/lib/systemd/system/kubelet.service.d/10-kubeadm.conf
```
重新加载systemd系统并重新启动kubelet服务。
```
systemctl daemon-reload
systemctl restart kubelet
```
现在我们准备配置Kubernetes集群。

### 第2步-Kubernetes集群初始化
在这一步中，我们将初始化kubernetes主集群配置。

将shell移至主服务器'k8s-master'并运行以下命令来设置kubernetes主服务器。
```sh
> kubeadm init --kubernetes-version=1.17.3 --apiserver-advertise-address=106.75.7.152 --pod-network-cidr=10.244.0.0/16

```
下载相关镜像
kubeadm 默认下载的镜像都是在 gcr.io 上的，国内的网络环境不能访问，我们可以先下下来，然后自己打tag。
```sh
> kubectl version 查看安装的版本
> kubeadm config images list 查看 kubeadm 会用到的镜像
k8s.gcr.io/kube-apiserver:v1.17.3
k8s.gcr.io/kube-controller-manager:v1.17.3
k8s.gcr.io/kube-scheduler:v1.17.3
k8s.gcr.io/kube-proxy:v1.17.3
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.4.3-0
k8s.gcr.io/coredns:1.6.5
```
使用如下命令拉取镜像，并打tag。
```sh
docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.17.3
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-apiserver-amd64:v1.17.3 k8s.gcr.io/kube-apiserver:v1.17.3

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.17.3
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-controller-manager-amd64:v1.17.3 k8s.gcr.io/kube-controller-manager:v1.17.3

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.17.3
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-scheduler-amd64:v1.17.3 k8s.gcr.io/kube-scheduler:v1.17.3

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.17.3
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/kube-proxy-amd64:v1.17.3 k8s.gcr.io/kube-proxy:v1.17.3

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/pause-amd64:3.1
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/pause-amd64:3.1 k8s.gcr.io/pause:3.1

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.4.3-0
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/etcd-amd64:3.4.3-0 k8s.gcr.io/etcd:3.4.3-0

docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.5
docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/coredns:1.6.5 k8s.gcr.io/coredns:1.6.5
```
注意：

--apiserver-advertise-address =确定Kubernetes应该在哪个IP地址上发布其API服务器。

--pod-network-cidr =指定Pod网络的IP地址范围。我们正在使用“flannel”虚拟网络。如果要使用其他网络，例如weave-net或calico，请更改范围IP地址。

Kubernetes初始化完成后，您将获得如下结果。

[![Kubernetes初始化完成](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/7.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/7.png)



**注意：**

将“ **kubeadm join ... ... ...** ”命令复制到文本编辑器。该命令将用于向kubernetes集群注册新节点。

现在，为了使用Kubernetes，我们需要根据结果运行一些命令。

创建新的“ .kube”配置目录并复制配置“ admin.conf”。

```
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

接下来，使用kubectl命令将绒布网络部署到kubernetes集群。

```
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

[![Kubernetes加入](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/8.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/8.png)

绒布网络已部署到Kubernetes集群。

等待一分钟，然后使用以下命令检查kubernetes节点和Pod。

```
kubectl get nodes
kubectl get pods --all-namespaces
```

您将获得“ k8s-master”节点作为状态为“ ready”的“ master”集群运行，并且将获得集群所需的所有Pod，包括网络Pod的“ kube-flannel-ds”组态。

确保所有kube系统吊舱状态为“正在运行”。

[![检查Kubernetes节点](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/9.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/9.png)

Kubernetes集群主机的初始化和配置已完成。

## 步骤3-将node01和node02添加到集群

在此步骤中，我们将添加node01和node02来加入“ k8s”集群。

连接到node01服务器并运行我们在顶部复制的kubeadm join命令。

```
kubeadm join 10.0.15.10:6443 --token vzau5v.vjiqyxq26lzsf28e --discovery-token-ca-cert-hash sha256:e6d046ba34ee03e7d55e1f5ac6d2de09fd6d7e6959d16782ef0778794b94c61e
```

[![将node01和node02添加到集群](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/10.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/10.png)

连接到node02服务器并运行我们在顶部复制的kubeadm join命令。

```
kubeadm join 10.0.15.10:6443 --token vzau5v.vjiqyxq26lzsf28e --discovery-token-ca-cert-hash sha256:e6d046ba34ee03e7d55e1f5ac6d2de09fd6d7e6959d16782ef0778794b94c61e
```

[![连接Docker节点](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/11.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/11.png)

等待几分钟，然后使用以下命令返回到“ k8s-master”主群集服务器，检查节点和吊舱。

```
kubectl get nodes
kubectl get pods --all-namespaces
```

现在，您将获得node01，并且node02已添加到状态为“就绪”的集群中。

[![kubctl命令](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/12.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/12.png)

node01和node02已添加到kubernetes集群中。

## 步骤4-测试创建第一个Pod

在这一步中，我们将通过将Nginx pod部署到kubernetes集群来进行测试。吊舱是一组或多个具有共享存储和网络的容器，它们在Kubernetes下运行。Pod包含一个或多个容器，例如Docker容器。

登录到“ k8s-master”服务器，并使用kubectl命令创建名为“ nginx”的新部署。

```
kubectl create deployment nginx --image=nginx
```

要查看“ nginx”部署分隔的详细信息，请运行以下命令。

```
kubectl describe deployment nginx
```

您将获得nginx pod部署规范。

接下来，我们将展示可通过互联网访问的Nginx Pod。为此，我们需要创建新的服务NodePort。

运行下面的kubectl命令。

```
kubectl create service nodeport nginx --tcp=80:80
```

[![创建第一个吊舱](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/13.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/13.png)

确保没有错误。现在，使用下面的kubectl命令检查nginx服务nodeport和IP。

```
kubectl get pods
kubectl get svc
```

[![获取豆荚列表](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/14.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/14.png)

现在，您将获得nginx pod，它现在在群集IP地址“ 10.160.60.38”端口80下运行，节点主IP地址“ 10.0.15.x”在端口“ 30691”下运行。

从“ k8s-master”服务器运行以下[curl命令](https://www.howtoforge.com/community/threads/how-to-install-curl.20374/) 。

```
curl node01:30691
```

[![卷曲测试](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/15.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/15.png)

```
curl node02:30691
```

[![卷曲测试节点2](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/16.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/16.png)

Nginx Pod现在已经部署在Kubernetes集群下，并且可以通过Internet访问。

现在从网络浏览器访问。

***http://10.0.15.10:30691/***

然后您将获得Nginx默认页面。

[![节点1上的页面](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/17.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/17.png)

在node02服务器上-http: ***//10.0.15.11:*** 30691/

[![节点2上的页面](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/18.png)](https://www.howtoforge.com/images/how_to_set_up_kubernetes_cluster_on_centos_7/big/18.png)

CentOS 7上的Kubernetes集群安装和配置已成功完成。