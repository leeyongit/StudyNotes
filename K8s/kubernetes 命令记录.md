kubernetes 命令记录
---
操作基本命令：
通过yaml文件创建：
```yaml
kubectl create -f xxx.yaml （不建议使用，无法更新，必须先delete）
kubectl apply -f xxx.yaml （创建+更新，可以重复使用）
```

通过yaml文件删除：
```yaml
kubectl delete -f xxx.yaml
```
查看kube-system namespace下面的pod/svc/deployment 等等（-o wide  选项可以查看存在哪个对应的节点）
```yaml
kubectl get pod/svc/deployment -n kube-system
```
查看所有namespace下面的pod/svc/deployment等等
```yaml
kubectl get pod/svc/deployment --all-namcpaces
```
重启pod（无法删除对应的应用，因为存在deployment/rc之类的副本控制器，删除pod也会重新拉起来）
```yaml
kubectl get pod -n kube-system
```
查看pod描述：
```yaml

 XXX -n kube-system
```
查看pod 日志 （如果pod有多个容器需要加-c 容器名）
```yaml
kubectl logs xxx -n kube-system
```
## 删除应用（先确定是由说明创建的，再删除对应的kind）：
```yaml
kubectl delete deployment xxx -n kube-system
```
## 根据label删除：
```yaml
kubectl delete pod -l app=flannel -n kube-system
```
## 扩容
```yaml
kubectl scale deployment spark-worker-deployment --replicas=8
```
## 导出配置文件：
导出proxy
```yaml
kubectl get ds -n kube-system -l k8s-app=kube-proxy -o yaml>kube-proxy-ds.yaml
```
导出kube-dns
```yaml
kubectl get deployment -n kube-system -l k8s-app=kube-dns -o yaml >kube-dns-dp.yaml
kubectl get services -n kube-system -l k8s-app=kube-dns -o yaml >kube-dns-services.yaml
```
导出所有 configmap
```yaml
kubectl get configmap -n kube-system -o wide -o yaml > configmap.yaml
```

## 复杂操作命令：
删除kube-system 下Evicted状态的所有pod：
```yaml
kubectl get pods -n kube-system |grep Evicted| awk '{print $1}'|xargs kubectl delete pod  -n kube-system
```


## 以下为维护环境相关命令：

重启kubelet服务
```yaml
systemctl daemon-reload
systemctl restart kubelet
```
修改启动参数
```yaml
vim /etc/systemd/system/kubelet.service.d/10-kubeadm.conf
```
查看集群信息
```yaml
kubectl cluster-info
```
查看各组件信息
```yaml
kubectl get componentstatuses
```
查看kubelet进程启动参数
```yaml
ps -ef | grep kubelet
```
查看日志:
```yaml
journalctl -u kubelet -f
```

设为不可调度状态：
```yaml
kubectl cordon node1
```
将pod赶到其他节点：
```yaml
kubectl drain node1
```
解除不可调度状态
```yaml
kubectl uncordon node1
```
master运行pod
```yaml
kubectl taint nodes master.k8s node-role.kubernetes.io/master-
```
master不运行pod
```yaml
kubectl taint nodes master.k8s node-role.kubernetes.io/master=:NoSchedule
```