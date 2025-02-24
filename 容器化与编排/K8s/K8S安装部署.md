Kubernetes (K8s) 的安装和部署可以根据场景选择不同的方法。以下提供几种常见的安装方法。

------

## **一、在本地测试环境安装 (使用 Minikube)**

### 1. **安装依赖**

- 安装 `kubectl`：

  ```bash
  curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
  chmod +x kubectl
  sudo mv kubectl /usr/local/bin/
  ```

  验证安装：

  ```bash
  kubectl version --client
  ```

- 安装 VirtualBox 或其他虚拟机管理工具：

  ```bash
  sudo apt install virtualbox
  ```

- 安装 Minikube：

  ```bash
  curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
  sudo install minikube-linux-amd64 /usr/local/bin/minikube
  ```

  验证安装：

  ```bash
  minikube version
  ```

### 2. **启动 Minikube**

启动集群：

```bash
minikube start --driver=virtualbox
```

验证集群状态：

```bash
kubectl get nodes
```

### 3. **访问 Dashboard**

```bash
minikube dashboard
```

会打开 Kubernetes 的 Dashboard 界面。

------

## **二、在生产环境部署 (Kubeadm 方法)**

### 1. **准备服务器**

需要至少两台服务器：

- 一台作为 Master 节点。
- 其他作为 Worker 节点。

操作系统建议使用 Ubuntu 20.04+ 或 CentOS 7/8。

### 2. **安装 Kubernetes 组件**

在所有节点上执行以下步骤。

#### 2.1 安装 Docker

```bash
sudo apt update
sudo apt install -y docker.io
sudo systemctl start docker
sudo systemctl enable docker
```

#### 2.2 安装 kubeadm、kubelet 和 kubectl

```bash
sudo apt update
sudo apt install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
cat <<EOF | sudo tee /etc/apt/sources.list.d/kubernetes.list
deb https://apt.kubernetes.io/ kubernetes-xenial main
EOF
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo systemctl enable kubelet
```

### 3. **初始化 Master 节点**

在 Master 节点上执行：

```bash
sudo kubeadm init --pod-network-cidr=10.244.0.0/16
```

#### 配置 kubectl

```bash
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

验证集群状态：

```bash
kubectl get nodes
```

#### 配置网络插件 (以 Flannel 为例)

```bash
kubectl apply -f https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml
```

### 4. **加入 Worker 节点**

在 Master 节点的初始化输出中会有一条命令，用于添加 Worker 节点，例如：

```bash
kubeadm join <master-ip>:6443 --token <token> --discovery-token-ca-cert-hash sha256:<hash>
```

在 Worker 节点上运行这条命令即可。

------

## **三、在云平台部署 Kubernetes**

如需简化部署，可以使用云服务提供的 Kubernetes 解决方案，如：

- **AWS**：Amazon Elastic Kubernetes Service (EKS)
- **Azure**：Azure Kubernetes Service (AKS)
- **Google Cloud**：Google Kubernetes Engine (GKE)

这些服务通常提供控制面板，直接管理 Kubernetes 集群，无需手动配置。

------

## **四、验证安装**

1. 验证所有节点：

   ```bash
   kubectl get nodes
   ```

2. 验证集群组件：

   ```bash
   kubectl get pods -n kube-system
   ```

