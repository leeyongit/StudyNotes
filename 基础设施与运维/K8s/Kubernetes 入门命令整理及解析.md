# Kubernetes 入门命令整理及解析

* Master（主节点）： 控制 Kubernetes 节点的机器，也是创建作业任务的地方。
* Node（节点）： 这些机器在 Kubernetes 主节点的控制下执行被分配的任务。
* Pod： 由一个或多个容器构成的集合，作为一个整体被部署到一个单一节点。同一个 pod 中的容器共享 IP 地址、进程间通讯（IPC）、主机名以及其它资源。Pod 将底层容器的网络和存储抽象出来，使得集群内的容器迁移更为便捷。
* Replication controller（复制控制器）： 控制一个 pod 在集群上运行的实例数量。
* Service（服务）： 将服务内容与具体的 pod 分离。Kubernetes 服务代理负责自动将服务请求分发到正确的 pod 处，不管 pod 移动到集群中的什么位置，甚至可以被替换掉。
* Kubelet： 这个守护进程运行在各个工作节点上，负责获取容器列表，保证被声明的容器已经启动并且正常运行。
* kubectl： 这是 Kubernetes 的命令行配置工具。

## 方便记忆的规律

kubernetes命令有一些相通的规律，可以帮助我们快速掌握。

-A，无论获取哪种资源，这个参数代表所有命名空间
-o wide 无论获取哪种资源，代表更详细的列出资源，一般看pod的ip，和对应的node节点比较常用。
打命令的时候一般分四种：get、delete、describe、logs，然后加上对应的资源类型即可

## GET 获取资源信息
```sh
kubectl get all # 查看所有，包括pod，svc 可以在后面加-A即--all-namespaces

kubectl get pods|deployments.|replicasets.|service -o wide #获取特定的资源类型

kubectl get nodes --show-labels # 查看node标签

kubectl api-versions # 查看k8s当前支持的api版本

kubectl logs pod_name # 查看pod内进程输出 (查看最后200行 kubectl logs --tail=200 pod_name 或最后1小时 --since=1h )

kubectl describe pods|deployments.|replicasets.|service xxx_name 描述特定资源特定实例

journalctl -l -u kubelet # 查看kubelet日志

kubectl get pods -o yaml # 配置yaml格式详情

kubectl get endpoints # 查看终端服务节点

kubectl top node node1 --v=8 #开启debug模式输出.使用top指令查看资源情况,需部署heapster(1.8之前版本默认提供.之后修改为service-metric).
```
## 更改资源信息
```sh
kubectl apply -f xxx.yml 依照某个资源文件申请更改

kubectl apply -f httpd.v1.yml --record # 登记到revision,用于回滚

kubectl rollout history deployment httpd # 查看历史记录,为回滚做准备

kubectl rollout undo deployment httpd --to-revision=1 # 回滚到版本1

kubectl edit daemonsets. kube-proxy --namespace=kube-system # 编辑内存里配置文件.应该是及时修改后生效,但测试并未生效

kubectl edit service nginx-svc # 修改service信息

kubectl scale --replicas=3 deploy myapp # 扩容
```
## 创建资源
```sh
kubectl create configmap game-config --from-file=docs/gameconfig/ # 目录内含有多个配置文件(每个配置文件里可以有很多内容)

kubectl expose deployment myapp --port=3380 --target-port=8080 # 暴露myapp deploy 为3380端口，默认轮训三个副本

kubectl run busybox --rm -it --image=busybox /bin/sh # 临时启用一个工具箱

kubectl run -it --rm --image=mysql:5.6 --restart=Never mysql-client -- mysql -h mysql -ppassword # 临时启动一个mysql客户端
```
## 删除资源
```yaml
kubectl delete pods|deployments.|replicasets.|service xxx_name

kubectl delete -f xxx.yml
```
## 特殊应用
```sh
kubectl exec mypod-id touch /mydata/hello # 到Pod的container里执行指令

kubectl exec mypod-id df | grep data # 到Pod里执行指令
```
想将deployment，封装为一个服务，可以一次执行一下命令
```sh
kubectl expose deployment myapp --port=3380 --target-port=8080 # 暴露myapp deploy 为3380端口，默认轮训三个副本
```
通过ipvsadm -ln 看到该ip分别轮训三个副本 kubectl get svc -o wide
```sh
kubectl edit svc myapp # 如果想外网访问该服务，修改service的type从ClusterIP为NodePort
```
就可以系统默认给了一个port，可以通过master(或node)的ip加上该port就可以访问到myapp服务

