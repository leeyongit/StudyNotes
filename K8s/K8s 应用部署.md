K8s 应用部署
---
## 概述
当希望在Kubernetes中部署应用程序时，通常要定义三个组件：
部署（Deployment）：这是创建Pod应用程序副本的方法
服务（Service）：将流量调度到Pods的内部负载平衡器
入口(Ingress)：描述流量如何从群集外部流到服务。

1. 部署应用
1.1 命令方式
```sh
> kubectl run httpd-app --image=httpd --replicas=3
```
通过命令行方式部署apache服务

1.2 配置文件方式
```sh
cat >> nginx.yml << EOF
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      restartPolicy: Always
      containers:
      - name: nginx
        image: nginx:latest
EOF
```
```sh
> kubectl apply -f nginx.yml
```
2. 状态查看
2.1 查看节点状态
```sh
> kubectl get nodes
```
2.2 查看pod状态
```sh
> kubectl get pod --all-namespaces
```
2.3 查看副本数
```sh
> kubectl get deployments
```
2.4 查看deployment详细信息
```sh
> kubectl describe deployments
```
2.5 查看集群基本组件状态
```sh
> kubectl get cs
```

### 测试创建第一个Pod

在这一步中，我们将通过将Nginx pod部署到kubernetes集群来进行测试。吊舱是一组或多个具有共享存储和网络的容器，它们在Kubernetes下运行。Pod包含一个或多个容器，例如Docker容器。

登录到"k8s-master"服务器，并使用kubectl命令创建名为"nginx"的新部署。

```
kubectl create deployment nginx --image=nginx
```

要查看"nginx"部署分隔的详细信息，请运行以下命令。

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