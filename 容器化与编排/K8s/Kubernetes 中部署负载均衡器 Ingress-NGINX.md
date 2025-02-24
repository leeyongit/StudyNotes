以下是 Kubernetes 中部署负载均衡器 `Ingress-NGINX` 的完整步骤：

------

## **前置条件**

1. Kubernetes 集群已经搭建完成，并可正常访问。
2. 已安装 `kubectl`，可以管理 Kubernetes 集群。
3. 确保集群中安装了一个网络插件（如 Flannel、Calico 等）。

------

## **部署 Ingress-NGINX**

### 1. **安装 Ingress-NGINX**

使用官方推荐的 YAML 文件来安装 Ingress-NGINX：

```bash
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/main/deploy/static/provider/cloud/deploy.yaml
```

安装后会创建以下资源：

- `Ingress Controller` 部署（通常为 `nginx-ingress-controller`）。
- `Service`，默认类型为 `LoadBalancer` 或 `NodePort`。
- 相关的 ConfigMap、ServiceAccount 和 RBAC 配置。

------

### 2. **验证安装是否成功**

1. 查看 Pod 状态：

   ```bash
   kubectl get pods -n ingress-nginx
   ```

   输出类似以下内容：

   ```
   NAME                                        READY   STATUS    RESTARTS   AGE
   ingress-nginx-controller-xxxxxxx-yyyyy      1/1     Running   0          5m
   ```

2. 查看 `Ingress-NGINX` 服务：

   ```bash
   kubectl get svc -n ingress-nginx
   ```

   输出类似以下内容：

   ```
   NAME                                 TYPE           CLUSTER-IP       EXTERNAL-IP      PORT(S)                      AGE
   ingress-nginx-controller             LoadBalancer   10.96.50.142     <pending>        80:30080/TCP,443:30443/TCP   5m
   ```

   - 如果是云平台，`EXTERNAL-IP` 会显示实际的负载均衡器 IP。

   - 如果是本地环境（如 Minikube），可以使用 

     ```
     minikube service
     ```

      查看负载均衡器的地址：

     ```bash
     minikube service ingress-nginx-controller -n ingress-nginx
     ```

------

### 3. **创建测试服务**

创建一个简单的 `Deployment` 和 `Service` 来进行测试：

#### 3.1 Deployment 和 Service 文件

创建 `test-deployment.yaml`：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: test-deployment
  labels:
    app: test
spec:
  replicas: 2
  selector:
    matchLabels:
      app: test
  template:
    metadata:
      labels:
        app: test
    spec:
      containers:
      - name: test-container
        image: nginx
        ports:
        - containerPort: 80
---
apiVersion: v1
kind: Service
metadata:
  name: test-service
spec:
  selector:
    app: test
  ports:
    - protocol: TCP
      port: 80
      targetPort: 80
```

应用文件：

```bash
kubectl apply -f test-deployment.yaml
```

------

### 4. **配置 Ingress 资源**

创建 `ingress-resource.yaml`：

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: test-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /
spec:
  rules:
  - host: test.local
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: test-service
            port:
              number: 80
```

应用文件：

```bash
kubectl apply -f ingress-resource.yaml
```

------

### 5. **测试 Ingress**

1. 如果是在本地环境（如 Minikube），需要配置 `/etc/hosts`：

   ```bash
   echo "$(minikube ip) test.local" | sudo tee -a /etc/hosts
   ```

2. 访问 `http://test.local`，可以看到 Nginx 默认页面。

------

### 6. **常见问题及解决方法**

1. **Pod 状态为 CrashLoopBackOff**
    确保网络插件（如 Flannel）已正确安装：

   ```bash
   kubectl get pods -n kube-system
   ```

2. **`EXTERNAL-IP` 一直为 `<pending>`**

   - 如果是本地测试，可以将 `ingress-nginx-controller` 的 `Service` 类型改为 `NodePort`。

   - 编辑 Service：

     ```bash
     kubectl edit svc ingress-nginx-controller -n ingress-nginx
     ```

     将 

     ```
     type: LoadBalancer
     ```

      修改为 

     ```
     type: NodePort
     ```

     。

3. **无法访问域名**

   - 检查 `/etc/hosts` 是否配置正确。
   - 确保 `Ingress` 资源的 `host` 和访问时的域名匹配。

------

