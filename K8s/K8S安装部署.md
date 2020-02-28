K8S安装部署
---

## 准备环境
```sh
# 关闭防火墙
> systemctl stop firewalld
> systemctl disable firewalld

# 关闭Selinux
> sed -i 's/enforcing/disabled/' /etc/selinux/config
> setenforce 0

# 关闭swap
> swapoff -a # 临时
> vim /etc/fstab # 永久

# 添加主机名与IP对应关系
> vim /etc/hosts
10.10.50.151 k8s-master
10.10.147.96 k8s-node1
```
## 安装docker-ce
以下适用于centos 7
```sh
# step 1: 安装必要的一些系统工具
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# Step 2: 添加软件源信息
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo
yum-config-manager --disable docker-ce-edge
yum-config-manager --disable docker-ce-test

# Step 3: 更新并安装 Docker-CE
sudo yum makecache fast
sudo yum -y install docker-ce

# Step 4: 开启Docker服务
sudo service docker start

# Step 5: 更改cgroup driver
cat > /etc/docker/daemon.json <<EOF
{
  "exec-opts": ["native.cgroupdriver=systemd"],
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "100m"
  },
  "storage-driver": "overlay2",
  "storage-opts": [
    "overlay2.override_kernel_check=true"
  ]
}
EOF

```


## 安装二进制文件
主要是安装kubelet、kubeadm以及kubectl这三个可执行文件。其中kubeadm是官方的安装工具，kubectl是客户端.
安装阿里云的k8s-yum源
以下是针对于CentOS的yum源，官方也有针对Ubuntu的源。
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

### 安装kubelet
如果你希望直接安装最新发布版本的k8s，请直接执行（最终安装的版本关键看你安装的kubeadm版本）。
```sh
setenforce 0
yum install -y kubelet-1.15.10 kubeadm-1.15.10 kubectl-1.15.10 --disableexcludes=kubernetes
systemctl enable docker && systemctl start docker
systemctl enable kubelet && systemctl start kubelet
```

调参运行
照着执行就行了。
```sh
cat <<EOF >  /etc/sysctl.d/k8s.conf
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
net.ipv4.ip_forward = 1
EOF
sysctl --system

systemctl daemon-reload
systemctl restart kubelet
```

### 容器组件
#### 拉取镜像
google和docker似乎是有意要对着干的，虽然阿里云也有docker registry的加速器，但是google并没有将kubernetes的镜像放到docker hub上。所以，我们需要先使用脚本，从阿里云的google_containers命名空间下载对应的克隆镜像，然后再通过docker tag将其labels修改为kubeadm生成的static-pod yaml文件对应的镜像标签。从而欺骗kubeadm，所有镜像都已经ready了，不用再去公网上拉取了。

具体操作如下：

#### 镜像列表
你肯定会疑问，我怎么知道我要使用哪些镜像？

好在v1.12.2以上的版本，kubeadm提示可以使用以下命令来获取到镜像信息：
```sh
[root@k8s-master manifests]# kubeadm config images list
8s.gcr.io/kube-apiserver:v1.15.10
k8s.gcr.io/kube-controller-manager:v1.15.10
k8s.gcr.io/kube-scheduler:v1.15.10
k8s.gcr.io/kube-proxy:v1.15.10
k8s.gcr.io/pause:3.1
k8s.gcr.io/etcd:3.3.10
k8s.gcr.io/coredns:1.3.1
```
从阿里云拉取镜像
```sh
[root@k8s-master manifests]# cat ./pull.sh
for i in `kubeadm config images list`; do
  imageName=${i#k8s.gcr.io/}
  docker pull registry.aliyuncs.com/google_containers/$imageName
  docker tag registry.aliyuncs.com/google_containers/$imageName k8s.gcr.io/$imageName
  docker rmi registry.aliyuncs.com/google_containers/$imageName
done;
```

### 安装k8s 启动主节点，启动集群
这就是kubeadm的安装流程了；下面是部署单节点k8s的命令，如果需要部署k8s集群，可以通过指定config文件的方式来指定其etcd集群，并使用相同的方式部署多个api-server、controller-manager以及scheduler。

#### k8s
```sh
> kubeadm init \
    --apiserver-advertise-address=10.10.50.151 \
    --kubernetes-version=1.15.10  \
    --service-cidr=10.1.0.0/16 \
    --pod-network-cidr=10.244.0.0/16
    #--kubernetes-version=$(kubeadm version -o short)  \
    #--image-repository=registry.aliyuncs.com/google_containers \
[init] using Kubernetes version: v1.12.2
[preflight] running pre-flight checks
[preflight/images] Pulling images required for setting up a Kubernetes cluster
[preflight/images] This might take a minute or two, depending on the speed of your internet connection
[preflight/images] You can also perform this action in beforehand using 'kubeadm config images pull'
```
如果一切无误，kubeadm最后会有形如以下的输出：
```sh
kubeadm join 10.10.50.151:6443 --token g3d6wt.m63nfwk55sgaosuv \
    --discovery-token-ca-cert-hash sha256:97b5e56e8aa7167937a1e6c0b0e4c5f03f24b05f97f608a9f46ae8c39aeb5036
```
记录上述输出，供从节点启动使用。

### 增加kubectl权限访问
以一般用户运行下列命令，配置主节点所在实例的kubectl
```sh
> mkdir -p $HOME/.kube
> sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
> sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### 配置CNI 安装pod网络组件
```sh
# 部署 flannel 作为 CNI
> kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/bc79dd1505b0c8681ece4de4c0d86c5cd2643275/Documentation/kube-flannel.yml
```
等待一分钟，然后使用以下命令检查kubernetes节点和Pod。

```
kubectl get nodes
kubectl get pods --all-namespaces
```
### （可选）让主节点也可以运行Pod
Kubernetes默认不在主节点上运行Pod，这里可以让调度器不再遵从这个策略。
这会提高资源利用率，代价是会降低主节点的安全性。
```sh
kubectl taint nodes --all node-role.kubernetes.io/master-
```

### 启动从节点，加入集群
增加node到集群里
```sh
// master初始化后的命令
kubeadm join 10.10.50.151:6443 --token uhvtb6.gdgzxibdqzcx1bp1 \
    --discovery-token-ca-cert-hash sha256:a6f0ad696dab736f417c192ccd339db0b2ab529bfb00a89428e70bf6fdeb4af8
```

master节点查看node状态
```sh
> kubectl get nodes
NAME           STATUS   ROLES    AGE   VERSION
10-10-50-151   Ready    master   35m   v1.17.3
```

部署webui dashboard
下载官方的yaml文件:
```sh
> wget https://raw.githubusercontent.com/kubernetes/dashboard/v1.10.1/src/deploy/recommended/kubernetes-dashboard.yaml
```
如果无法下载墙外镜像，修改image信息：

k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
=> ljchen/k8s_gcr_io_kubernetes-dashboard-amd64:v1.10.1
修改两个地方
第一个是修改镜像,kubernetes-dashboard.yaml配置文件112行
```yaml
108 k8s-app: kubernetes-dashboard
109 spec:
110 containers:
111 - name: kubernetes-dashboard
112 #image: k8s.gcr.io/kubernetes-dashboard-amd64:v1.10.1
113 image: mirrorgooglecontainers/kubernetes-dashboard-amd64:v1.10.0
114 ports:
115 - containerPort: 8443
116 protocol: TCP
```
第二个是添加一个type,指定端口类型为 NodePort，这样外界可以通过地址 nodeIP:nodePort 访问 dashboard,kubernetes-dashboard.yaml配置文件158行:
```yaml
148 # ------------------- Dashboard Service ------------------- #
149
150 kind: Service
151 apiVersion: v1
152 metadata:
153 labels:
154 k8s-app: kubernetes-dashboard
155 name: kubernetes-dashboard
156 namespace: kube-system
157 spec:
158 type: NodePort
159 ports:
160 - port: 443
161 targetPort: 8443
162 selector:
163 k8s-app: kubernetes-dashboard
```
### 安装dashboard
从官网拷贝dashboard的yaml文件到本地，保存为dashboard.yaml，需要注意版本。
```sh
> kubectl apply -f kubernetes-dashboard.yaml
```
查看kube-system namespace下面的dashboard pod
```sh
> kubectl get pods -n kube-system |grep dashboard
```
查看kube-system namespace下面的pod/svc
```sh
> kubectl get pods,svc -n kube-system
```

### Token （令牌） 认证方式登录
1）授权 (所有 namespace )
```sh
> kubectl create serviceaccount k8sadmin -n kube-system
> kubectl create clusterrolebinding k8sadmin --clusterrole=cluster-admin --serviceaccount=kube-system:k8sadmin
```
获取令牌（用于网页登录）
```sh
> kubectl -n kube-system describe secret $(kubectl -n kube-system get secret| awk '/k8sadmin/{print $1}')
eyJhbGciOiJSUzI1NiIsImtpZCI6IiJ9.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJrOHNhZG1pbi10b2tlbi1qZmZ4cCIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50Lm5hbWUiOiJrOHNhZG1pbiIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VydmljZS1hY2NvdW50LnVpZCI6IjgwMmM3NTZiLWExMjgtNGIxNS05NWYyLTY4YjA5MmYwZTI5ZiIsInN1YiI6InN5c3RlbTpzZXJ2aWNlYWNjb3VudDprdWJlLXN5c3RlbTprOHNhZG1pbiJ9.S1cXpBpMRQolXnKCCuqNievApv9TrcsOWjeAAVUp9Pdc00brBPNZ6PnQPlDARmdkjjChKgewYHUhuVzOmIXDFF3T1IirXU1_0SZyYpiiNctdzANXZh1A1QLlCel3s5TWnpAVa5d_qzpc1E0jL3VybHTmVBDESh7HwAqFxiEXQmmHEpjvpGY0ubtzVbd2AVd74hXqH7JvkXH1UiJKRODPCj2m-QPSrYexWHcM38OAxSmspzbAlhl765MI3heoPm_a0fNF5W_2uXG0Y6n-aPg0juUQLs4ef_umKByThsF9CTWxLAWfIwWdw8U6OZ_BSOoUTQJO1ecDdZ0LZSg_ixLMSw
```
3）将获取到的token放在令牌里
通过https访问ui `https://106.75.7.152:31416`



