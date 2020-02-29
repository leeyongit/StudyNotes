k8s中负载均衡器【ingress-nginx】部署
---

在Kubernetes中，服务和Pod的IP地址仅可以在集群网络内部使用，对于集群外的应用是不可见的。为了使外部的应用能够访问集群内的服务，在Kubernetes 目前 提供了以下几种方案：

- NodePort
- LoadBalancer
- Ingress

本节主要就ingress和ingress控制器ingress-nginx-controller的部署作简单介绍和记录。

**以下系统组件版本：**

[**云服务器**](https://cloud.tencent.com/product/cvm?from=10680)**：centos版本7.6.1810、k8s版本1.15.0、docker版本18.06.1-ce、ingress-nginx-controller版本0.25.0**

**Ingress**

1. Ingress 组成？
   - 将Nginx的配置抽象成一个Ingress对象，每添加一个新的服务只需写一个新的Ingress的yaml文件即可
   - 将新加入的Ingress转化成Nginx的配置文件并使之生效
   - ingress controller
   - ingress服务
2. Ingress 工作原理?
   - ingress controller通过和kubernetes api交互，动态的去感知集群中ingress规则变化，
   - 然后读取它，按照自定义的规则，规则就是写明了哪个域名对应哪个service，生成一段nginx配置，
   - 再写到nginx-ingress-controller的pod里，这个Ingress controller的pod里运行着一个Nginx服务，控制器会把生成的nginx配置写入/etc/nginx.conf文件中，
   - 然后reload一下使配置生效。 以此达到域名分配置和动态更新的问题。
3. Ingress 可以解决什么问题？
   - 动态配置服务 如果按照传统方式, 当新增加一个服务时, 我们可能需要在流量入口加一个反向代理指向我们新的服务. 而如果用了Ingress, 只需要配置好这个服务, 当服务启动时, 会自动注册到Ingress的中, 不需要而外的操作.
   - 减少不必要的端口暴露 配置过k8s的都清楚, 第一步是要关闭防火墙的, 主要原因是k8s的很多服务会以NodePort方式映射出去, 这样就相当于给[宿主机](https://cloud.tencent.com/product/cdh?from=10680)打了很多孔, 既不安全也不优雅. 而Ingress可以避免这个问题, 除了Ingress自身服务可能需要映射出去, 其他服务都不要用NodePort方式
4. **Ingress当前的实现方式？**
ingress-nginx-controller

部署
```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/ingress-nginx/nginx-0.25.0/deploy/static/mandatory.yaml
# docker pull registry.cn-hangzhou.aliyuncs.com/google_containers/nginx-ingress-controller:0.25.0
# docker tag registry.cn-hangzhou.aliyuncs.com/google_containers/nginx-ingress-controller:0.25.0 quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.25.0
```

检查服务
```sh
> kubectl get pod -n ingress-nginx
> kubectl get pods --all-namespaces -l app.kubernetes.io/name=ingress-nginx --watch
```