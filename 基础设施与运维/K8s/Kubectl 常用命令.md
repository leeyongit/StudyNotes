以下是 Kubernetes 中 `kubectl` 的常用命令汇总，涵盖常见操作及管理任务：

------

## **1. 集群信息**

- 查看集群信息：

  ```bash
  kubectl cluster-info
  ```

- 查看当前上下文（当前使用的集群）：

  ```bash
  kubectl config current-context
  ```

- 列出所有上下文：

  ```bash
  kubectl config get-contexts
  ```

- 切换上下文：

  ```bash
  kubectl config use-context <context-name>
  ```

------

## **2. 节点管理**

- 查看所有节点：

  ```bash
  kubectl get nodes
  ```

- 查看节点详细信息：

  ```bash
  kubectl describe node <node-name>
  ```

------

## **3. Pod 管理**

- 查看所有 Pod：

  ```bash
  kubectl get pods
  ```

- 查看某个命名空间下的所有 Pod：

  ```bash
  kubectl get pods -n <namespace>
  ```

- 查看 Pod 详细信息：

  ```bash
  kubectl describe pod <pod-name>
  ```

- 查看 Pod 日志：

  ```bash
  kubectl logs <pod-name>
  ```

- 查看指定容器的日志（如果 Pod 有多个容器）：

  ```bash
  kubectl logs <pod-name> -c <container-name>
  ```

- 进入 Pod 容器的交互式终端：

  ```bash
  kubectl exec -it <pod-name> -- /bin/bash
  ```

- 删除 Pod：

  ```bash
  kubectl delete pod <pod-name>
  ```

------

## **4. Deployment 管理**

- 查看所有 Deployment：

  ```bash
  kubectl get deployments
  ```

- 查看 Deployment 详细信息：

  ```bash
  kubectl describe deployment <deployment-name>
  ```

- 更新 Deployment（例如更新镜像）：

  ```bash
  kubectl set image deployment/<deployment-name> <container-name>=<new-image>
  ```

- 扩容/缩容 Deployment：

  ```bash
  kubectl scale deployment <deployment-name> --replicas=<number>
  ```

- 删除 Deployment：

  ```bash
  kubectl delete deployment <deployment-name>
  ```

------

## **5. Service 管理**

- 查看所有 Service：

  ```bash
  kubectl get svc
  ```

- 查看 Service 详细信息：

  ```bash
  kubectl describe svc <service-name>
  ```

- 删除 Service：

  ```bash
  kubectl delete svc <service-name>
  ```

------

## **6. Namespace 管理**

- 查看所有命名空间：

  ```bash
  kubectl get namespaces
  ```

- 切换命名空间（默认命名空间为 `default`）：

  ```bash
  kubectl config set-context --current --namespace=<namespace-name>
  ```

- 创建命名空间：

  ```bash
  kubectl create namespace <namespace-name>
  ```

- 删除命名空间：

  ```bash
  kubectl delete namespace <namespace-name>
  ```

------

## **7. 配置文件操作**

- 应用 YAML 配置文件：

  ```bash
  kubectl apply -f <file.yaml>
  ```

- 删除资源（基于 YAML 文件）：

  ```bash
  kubectl delete -f <file.yaml>
  ```

- 验证 YAML 文件：

  ```bash
  kubectl apply --dry-run=client -f <file.yaml>
  ```

------

## **8. 排查问题**

- 查看所有资源的状态：

  ```bash
  kubectl get all
  ```

- 查看事件日志：

  ```bash
  kubectl get events
  ```

- 查看 Pod 的详细状态（包括事件）：

  ```bash
  kubectl describe pod <pod-name>
  ```

------

## **9. 常见的 YAML 文件生成**

- 生成 Deployment 的模板：

  ```bash
  kubectl create deployment <deployment-name> --image=<image> --dry-run=client -o yaml > deployment.yaml
  ```

- 生成 Service 的模板：

  ```bash
  kubectl create service clusterip <service-name> --tcp=<port>:<target-port> --dry-run=client -o yaml > service.yaml
  ```

------

## **10. 高级命令**

- 查看资源的 YAML/JSON 定义：

  ```bash
  kubectl get <resource> <resource-name> -o yaml
  kubectl get <resource> <resource-name> -o json
  ```

- 滚动更新 Deployment：

  ```bash
  kubectl rollout restart deployment/<deployment-name>
  ```

- 查看 Deployment 更新历史：

  ```bash
  kubectl rollout history deployment/<deployment-name>
  ```

- 回滚 Deployment 到上一版本：

  ```bash
  kubectl rollout undo deployment/<deployment-name>
  ```

------

