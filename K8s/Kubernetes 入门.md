Kubernetes 入门
---
Master（主节点）： 控制 Kubernetes 节点的机器，也是创建作业任务的地方。
Node（节点）： 这些机器在 Kubernetes 主节点的控制下执行被分配的任务。
Pod： 由一个或多个容器构成的集合，作为一个整体被部署到一个单一节点。同一个 pod 中的容器共享 IP 地址、进程间通讯（IPC）、主机名以及其它资源。Pod 将底层容器的网络和存储抽象出来，使得集群内的容器迁移更为便捷。
Replication controller（复制控制器）： 控制一个 pod 在集群上运行的实例数量。
Service（服务）： 将服务内容与具体的 pod 分离。Kubernetes 服务代理负责自动将服务请求分发到正确的 pod 处，不管 pod 移动到集群中的什么位置，甚至可以被替换掉。
Kubelet： 这个守护进程运行在各个工作节点上，负责获取容器列表，保证被声明的容器已经启动并且正常运行。
kubectl： 这是 Kubernetes 的命令行配置工具。









