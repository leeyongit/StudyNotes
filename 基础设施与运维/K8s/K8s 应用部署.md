以下是 Kubernetes (K8s) 应用部署的教程，主要分为以下步骤：

------

### 1. **准备环境**

确保已安装以下工具：

- **kubectl**: Kubernetes 的命令行工具。
- **minikube 或 Kubernetes 集群**: 本地测试可用 minikube，生产环境需要云端或自建集群。
- **Docker**: 用于构建和推送容器镜像。

------

### 2. **构建并推送容器镜像**

1. 编写 Dockerfile：

   ```dockerfile
   FROM node:18
   WORKDIR /app
   COPY . .
   RUN npm install
   CMD ["npm", "start"]
   EXPOSE 3000
   ```

2. 构建镜像：

   ```bash
   docker build -t your-dockerhub-username/your-app-name:tag .
   ```

3. 推送镜像到 Docker Hub：

   ```bash
   docker push your-dockerhub-username/your-app-name:tag
   ```

------

### 3. **编写 K8s 部署文件**

创建以下 YAML 文件：

#### 3.1 Deployment 配置

`deployment.yaml`：

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: your-app-deployment
  labels:
    app: your-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: your-app
  template:
    metadata:
      labels:
        app: your-app
    spec:
      containers:
      - name: your-app
        image: your-dockerhub-username/your-app-name:tag
        ports:
        - containerPort: 3000
```

#### 3.2 Service 配置

`service.yaml`：

```yaml
apiVersion: v1
kind: Service
metadata:
  name: your-app-service
spec:
  selector:
    app: your-app
  ports:
    - protocol: TCP
      port: 80
      targetPort: 3000
  type: NodePort
```

------

### 4. **部署到 Kubernetes 集群**

1. 应用 Deployment：

   ```bash
   kubectl apply -f deployment.yaml
   ```

2. 应用 Service：

   ```bash
   kubectl apply -f service.yaml
   ```

3. 查看 Pod 和 Service 状态：

   ```bash
   kubectl get pods
   kubectl get svc
   ```

------

### 5. **验证部署**

1. 获取 Service 的 NodePort：

   ```bash
   kubectl describe svc your-app-service
   ```

   查看 

   ```
   NodePort
   ```

    值。

2. 访问应用： 在浏览器中访问 `http://<NodeIP>:<NodePort>`。

------

### 6. **扩展与更新**

#### 6.1 扩容 Pod

```bash
kubectl scale deployment your-app-deployment --replicas=5
```

#### 6.2 更新镜像

修改 `deployment.yaml` 中的镜像标签，然后执行：

```bash
kubectl apply -f deployment.yaml
```

------

### 7. **清理资源**

当完成实验后，可以清理资源：

```bash
kubectl delete -f service.yaml
kubectl delete -f deployment.yaml
```

------

如果有具体问题，可以详细说明，我会为你解答！