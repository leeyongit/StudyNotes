CentOS 安装Helm
---
1. 安装helm
```sh
wget https://storage.googleapis.com/kubernetes-helm/helm-v2.12.1-linux-amd64.tar.gz
tar -zxvf helm-v2.12.1-linux-amd64.tar.gz
cd linux-amd64/
# 拷贝helm到 /usr/local/bin
cp helm /usr/local/bin
```
2. 验证helm
```sh
helm version
Client: &version.Version{SemVer:"v2.12.1", GitCommit:"02a47c7249b1fc6d8fd3b94e6b4babf9d818144e", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.12.0", GitCommit:"d325d2a9c179b33af1a024cdb5a4472b6288016a", GitTreeState:"clean"}
```
如果版本不同的话会有问题
```
helm get cs
Error: incompatible versions client[v2.10.0] server[v2.9.0]
```
解决方案：
```sh
helm init --upgrade
```
初始化helm
```sh
helm init --client-only
```
可以执行helm push
```sh
helm plugin install https://github.com/chartmuseum/helm-push
```
更新repo
```sh
helm repo remove stable
repo remove local
# google 的repo需要翻墙。我们用公司内部的repo，大家可以使用阿里的
helm repo add ali https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```
搜索一个chart
```sh
helm search nginx
```
