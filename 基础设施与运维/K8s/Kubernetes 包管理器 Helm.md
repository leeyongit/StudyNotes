# Kubernetes 包管理器 Helm

**Helm** 帮助您管理 Kubernetes 应用程序——Helm Charts 帮助您定义、安装和升级最复杂的 Kubernetes 应用程序。

### Helm 是什么？？

Helm 是 Kubernetes 的包管理器。包管理器类似于我们在 Ubuntu 中使用的apt、Centos中使用的yum 或者Python中的 pip 一样，能快速查找、下载和安装软件包。Helm 由客户端组件 helm 和服务端组件 Tiller 组成, 能够将一组K8S资源打包统一管理, 是查找、共享和使用为Kubernetes构建的软件的最佳方式。

### Helm 解决了什么痛点？

在 Kubernetes中部署一个可以使用的应用，需要涉及到很多的 Kubernetes 资源的共同协作。比如你安装一个 WordPress 博客，用到了一些 Kubernetes (下面全部简称k8s)的一些资源对象，包括 Deployment 用于部署应用、Service 提供服务发现、Secret 配置 WordPress 的用户名和密码，可能还需要 pv 和 pvc 来提供持久化服务。并且 WordPress 数据是存储在mariadb里面的，所以需要 mariadb 启动就绪后才能启动 WordPress。这些 k8s 资源过于分散，不方便进行管理，直接通过 kubectl 来管理一个应用，你会发现这十分蛋疼。
 所以总结以上，我们在 k8s 中部署一个应用，通常面临以下几个问题：

- 如何统一管理、配置和更新这些分散的 k8s 的应用资源文件
- 如何分发和复用一套应用模板
- 如何将应用的一系列资源当做一个软件包管理



### Helm 相关组件及概念

Helm 包含两个组件，分别是 helm 客户端 和 Tiller 服务器：

- **helm** 是一个命令行工具，用于本地开发及管理chart，chart仓库管理等
- **Tiller** 是 Helm 的服务端。Tiller 负责接收 Helm 的请求，与 k8s 的 apiserver 交互，根据chart 来生成一个 release 并管理 release
- **chart** Helm的打包格式叫做chart，所谓chart就是一系列文件, 它描述了一组相关的 k8s 集群资源
- **release** 使用 helm install 命令在 Kubernetes 集群中部署的 Chart 称为 Release
- Repoistory Helm chart 的仓库，Helm 客户端通过 HTTP 协议来访问存储库中 chart 的索引文件和压缩包

### Helm 原理

下面两张图描述了 Helm 的几个关键组件 Helm（客户端）、Tiller（服务器）、Repository（Chart 软件仓库）、Chart（软件包）之间的关系以及它们之间如何通信

![helm 组件通信](/Users/liyong/Library/Application Support/typora-user-images/image-20200817232514466.png)



![image-20200817232542071](/Users/liyong/Library/Application Support/typora-user-images/image-20200817232542071.png)



**创建release**

- helm 客户端从指定的目录或本地tar文件或远程repo仓库解析出chart的结构信息
- helm 客户端指定的 chart 结构和 values 信息通过 gRPC 传递给 Tiller
- Tiller 服务端根据 chart 和 values 生成一个 release
- Tiller 将install release请求直接传递给 kube-apiserver

**删除release**

- helm 客户端从指定的目录或本地tar文件或远程repo仓库解析出chart的结构信息
- helm 客户端指定的 chart 结构和 values 信息通过 gRPC 传递给 Tiller
- Tiller 服务端根据 chart 和 values 生成一个 release
- Tiller 将delete release请求直接传递给 kube-apiserver

**更新release**

- helm 客户端将需要更新的 chart 的 release 名称 chart 结构和 value 信息传给 Tiller
- Tiller 将收到的信息生成新的 release，并同时更新这个 release 的 history
- Tiller 将新的 release 传递给 kube-apiserver 进行更新



### chart 的基本结构

Helm的打包格式叫做chart，所谓chart就是一系列文件, 它描述了一组相关的 k8s 集群资源。Chart中的文件安装特定的目录结构组织, 最简单的chart 目录如下所示：

![image-20200817232629946](/Users/liyong/Library/Application Support/typora-user-images/image-20200817232629946.png)



> charts 目录存放依赖的chart
>
> Chart.yaml 包含Chart的基本信息，包括chart版本，名称等
>
> templates 目录下存放应用一系列 k8s 资源的 yaml 模板
>
> _helpers.tpl 此文件中定义一些可重用的模板片断，此文件中的定义在任何资源定义模板中可用
>
> NOTES.txt 介绍chart 部署后的帮助信息，如何使用chart等
>
> values.yaml 包含了必要的值定义（默认值）, 用于存储 templates 目录中模板文件中用到变量的值



### 安装Helm

Helm 提供了几种安装方式，本文提供两种安装方式，想要查看更多安装方式，请阅读 Helm 的[官方文档](https://links.jianshu.com/go?to=https%3A%2F%2Fdocs.helm.sh%2Fusing_helm%2F%23installing-helm)：

- 手动安装方式

```ruby
$ 下载 Helm 二进制文件
$ wget https://storage.googleapis.com/kubernetes-helm/helm-v2.9.1-linux-amd64.tar.gz
$ 解压缩
$ tar -zxvf helm-v2.9.1-linux-amd64.tar.gz
$ 复制 helm 二进制 到bin目录下
$cp linux-amd64/helm /usr/local/bin/
```

- 使用官方提供的脚本一键安装

```ruby
$ curl https://raw.githubusercontent.com/kubernetes/helm/master/scripts/get > get_helm.sh
$ chmod 700 get_helm.sh
$ ./get_helm.sh
```



### 安装 Tiller

安装好 helm 客户端后，就可以通过以下命令将 Tiller 安装在 kubernetes 集群中：

```kotlin
helm init
```

这个地方默认使用 “[https://kubernetes-charts.storage.googleapis.com](https://links.jianshu.com/go?to=https%3A%2F%2Fkubernetes-charts.storage.googleapis.com)” 作为缺省的 stable repository 的地址，但由于国内有一张无形的墙的存在，[googleapis.com](https://links.jianshu.com/go?to=http%3A%2F%2Fgoogleapis.com) 是不能访问的。可以使用阿里云的源来配置：

```kotlin
helm init --upgrade -i registry.cn-hangzhou.aliyuncs.com/google_containers/tiller:v2.9.1  --stable-repo-url https://kubernetes.oss-cn-hangzhou.aliyuncs.com/charts
```

执行上面命令后，可以通过 kubectl get po -n kube-system 来查看 tiller 的安装情况。

由于 kubernetes 从1.6 版本开始加入了 RBAC 授权。当前的 Tiller 没有定义用于授权的 ServiceAccount， 访问 API Server 时会被拒绝，需要给 Tiller 加入授权。

- 创建 Kubernetes 的服务帐号和绑定角色

```sh
$ kubectl create serviceaccount --namespace kube-system tiller                               
serviceaccount "tiller" created

$ kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller
clusterrolebinding.rbac.authorization.k8s.io "tiller-cluster-rule" created
```

- 给 Tiller 的 deployments 添加刚才创建的 ServiceAccount

```sh
# 给 Tiller 的 deployments 添加刚才创建的 ServiceAccount
$ kubectl patch deploy --namespace kube-system tiller-deploy -p '{"spec":{"template":{"spec":{"serviceAccount":"tiller"}}}}'
deployment.extensions "tiller-deploy" patched
```

- 查看 Tiller deployments 资源是否绑定 ServiceAccount

```sh
$ kubectl get deploy -n kube-system tiller-deploy -o yaml | grep serviceAccount
serviceAccount: tiller
serviceAccountName: tiller
```

- 查看 Tiller 是否安装成功

```sh
$ helm version 
Client: &version.Version{SemVer:"v2.9.1", GitCommit:"20adb27c7c5868466912eebdf6664e7390ebe710", GitTreeState:"clean"}
Server: &version.Version{SemVer:"v2.9.1", GitCommit:"20adb27c7c5868466912eebdf6664e7390ebe710", GitTreeState:"clean"}
```

安装成功后，即可使用 helm install xxx 来安装 helm 应用。如果需要删除 Tiller，可以通过 kubectl delete deployment tiller-deploy --namespace kube-system  来删除 Tiller 的 deployment 或者使用 helm reset 来删除。

### 使用 Helm 操作 Chart

这一节将介绍如何使用 helm 来操作 chart，包括创建、删除、打包、安装等使用。

先介绍一下 Helm 的核心命令：

- helm create 创建一个 Chart 模板

```bash
$ helm create test
Creating test
```

- helm package 打包一个 Chart 模板

```bash
$ helm package test                                                                          
Successfully packaged chart and saved it to: /root/test-0.1.0.tgz
```

- helm search 查找可用的 Chart 模板

```ruby
$ helm search nginx                                  
NAME                    CHART VERSION   APP VERSION DESCRIPTION
stable/nginx-ingress    0.9.5           0.10.2      An nginx Ingress controller that uses ConfigMap...
stable/nginx-lego       0.3.1                       Chart for nginx-ingress-controller and kube-lego
stable/gcloud-endpoints 0.1.0                       Develop, deploy, protect and monitor your APIs ...
```

- helm inspect 查看指定 Chart 的基本信息

```yaml
apiVersion: v1
appVersion: "1.0"
description: A Helm chart for Kubernetes
name: test
version: 0.1.0

略...(省略一大段信息）
```

- helm install 根据指定的 Chart 部署一个 Release 到 Kubernetes 集群

### Chart 模板示例

#### Chart 文件结构

```css
wordpress
├── charts
├── Chart.yaml
├── README.md
├── requirements.lock
├── requirements.yaml
├── templates
│   ├── deployment.yaml
│   ├── externaldb-secrets.yaml
│   ├── _helpers.tpl
│   ├── ingress.yaml
│   ├── NOTES.txt
│   ├── pvc.yaml
│   ├── secrets.yaml
│   ├── svc.yaml
│   └── tls-secrets.yaml
└── values.yaml
```

一个 wordpress chart 如上（去除部分 test 和 charts 依赖）， 基本结构由以下几个部分组成：

- charts 存放子Chart (Subchart) 的定义，Subchart 指的是当前 Chart 依赖的 Chart ， 在 requirements.yaml 中定义
- Chart.yaml 包含 Chart 信息的 YAML 文件， 包括 Chart 的版本、名称等，在 DCE Helm 插件中还包含 Chart 的 **团队授权** 信息 和 **是否公开** 的信息
- README.md 可选：Chart 的介绍信息等（该文件对于一个大型 Chart 来说十分重要）
- Requirements.yaml 可选：列举当前 Chart 的需要依赖的 Chart
- templates
  - 该目录下存放 Chart 所有的 K8s 资源定义模板，通常不同的资源放在不同的文件中，DCE Helm 插件中自定义模板的 K8s 资源统一放在 all_sources.yaml 文件中
  - _helpers.tpl ， 通常这个文件存放可重用的模板片段，该文件中的定义可以在 Chart 其它资源定义模板中使用
  - NOTES.txt，可选：一段简短使用说明的文本文件，用于安装 Release 后提示用户使用
- values.yaml 当前 Chart 的默认配置的值

#### 编写一个简单的 Chart 示例

本节以构建一个名称为 nginx-test Chart 为示例，来描述一个 chart 必要条件。
 1、Chart.yaml 文件是 一个 chart 必要文件， 该文件可以简单包括以下字段（具体字段请参考[Helm官网](https://links.jianshu.com/go?to=https%3A%2F%2Fhelm.sh%2F))



```yaml
apiVersion: v1  (chart 的API版本, 总是"v1", 必要)
name: hello     (chart 的名称, 必要)
version: 0.0.1  (chart 的版本，这个版本必须必要遵循 SemVer 2标准)
description: A Helm chart for Kubernetes   (chart 模板的简介描述)
...
下面省略一些字段，默认情况下有这几个字段定义就可以了
```

2、values.yaml 文件是 chart 的必要文件，以 nginx 为示例：



```yaml
# Default values for test.
# This is a YAML-formatted file.
# Declare variables to be passed into your templates.
replicaCount: 1

image:
  repository: nginx
  tag: stable
  pullPolicy: IfNotPresent
service:
  type: ClusterIP
  port: 80

ingress:
  enabled: false
  annotations: {}
    # kubernetes.io/ingress.class: nginx
    # kubernetes.io/tls-acme: "true"
  path: /
  hosts:
    - chart-example.local
  tls: []
  #  - secretName: chart-example-tls
  #    hosts:
  #      - chart-example.local

resources: {}
  # We usually recommend not to specify default resources and to leave this as a conscious
  # choice for the user. This also increases chances charts run on environments with little
  # resources, such as Minikube. If you do want to specify resources, uncomment the following
  # lines, adjust them as necessary, and remove the curly braces after 'resources:'.
  # limits:
  #  cpu: 100m
  #  memory: 128Mi
  # requests:
  #  cpu: 100m
  #  memory: 128Mi

nodeSelector: {}

tolerations: []

affinity: {}
```

从示例中可以看出，values.yaml 中定义了一些当前chart 的一些默认值，用于 templates 下的 K8s 资源 yaml 渲染时填充默认值。不过需要注意的是，如果使用 helm install 来部署一个 Release , 可以通过下面命令指定一份yaml 文件作为填充值：

```bash
$ helm install --values=myvals.yaml nginx
```

3、创建 templates 下的模板文件， 用于生成 Kubernetes 资源清单(manifests) 如下所示:

```yaml
# nginx-test/templates/deployments.yaml 
apiVersion: apps/v1beta2
kind: Deployment
metadata:
  name: {{ template "nginx-test.fullname" . }}
  labels:
    app: {{ template "nginx-test.name" . }}
    chart: {{ template "nginx-test.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  replicas: {{ .Values.replicaCount }}
  selector:
    matchLabels:
      app: {{ template "nginx-test.name" . }}
      release: {{ .Release.Name }}
  template:
    metadata:
      labels:
        app: {{ template "nginx-test.name" . }}
        release: {{ .Release.Name }}
    spec:
      containers:
        - name: {{ .Chart.Name }}
          image: "{{ .Values.image.repository }}:{{ .Values.image.tag }}"
          imagePullPolicy: {{ .Values.image.pullPolicy }}
          ports:
            - name: http
              containerPort: 80
              protocol: TCP
          livenessProbe:
            httpGet:
              path: /
              port: http
          readinessProbe:
            httpGet:
              path: /
              port: http
          resources:
{{ toYaml .Values.resources | indent 12 }}
    {{- with .Values.nodeSelector }}
      nodeSelector:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.affinity }}
      affinity:
{{ toYaml . | indent 8 }}
    {{- end }}
    {{- with .Values.tolerations }}
      tolerations:
{{ toYaml . | indent 8 }}
    {{- end }}
    
# nginx-test/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ template "nginx-test.fullname" . }}
  labels:
    app: {{ template "nginx-test.name" . }}
    chart: {{ template "nginx-test.chart" . }}
    release: {{ .Release.Name }}
    heritage: {{ .Release.Service }}
spec:
  type: {{ .Values.service.type }}
  ports:
    - port: {{ .Values.service.port }}
      targetPort: http 
      protocol: TCP
      name: http
  selector:
    app: {{ template "nginx-test.name" . }}
    release: {{ .Release.Name }}
```

上面定义了 一个 deployments.yaml 和 service.yaml 资源文件，里面使用 {{ }} 符号的是 Go 模板语言的标准。其中可以通过：

- .Values 对象访问 values.yaml 文件的内容， 前面的dot(.) 表示从顶层命名空间开始，找到 Values 对象(下同)
- .Release、.Chart 开头的预定义值可用于任何的模板中
- .Chart 对象用来访问 Chart.yaml 文件的内容
- .Release 对象是 Helm的内置对象之一， 使用 Helm 安装一个 release 时，由 Tiller 分配 release 的名称

4、命名模板(_helper.tpl) ：可以从上面看到有 {{ template "nginx-test.fullname" . }} 定义。该定义由 _helper.tpl 文件定义的字段来实现，比如下面一个 _helper.tpl :



```ini
{{/* vim: set filetype=mustache: */}}
{{/*
Expand the name of the chart.
*/}}
{{- define "nginx-test.name" -}}
{{- default .Chart.Name .Values.nameOverride | trunc 63 | trimSuffix "-" -}}
{{- end -}}

{{/*
Create a default fully qualified app name.
We truncate at 63 chars because some Kubernetes name fields are limited to this (by the DNS naming spec).
If release name contains chart name it will be used as a full name.
*/}}
{{- define "nginx-test.fullname" -}}
{{- if .Values.fullnameOverride -}}
{{- .Values.fullnameOverride | trunc 63 | trimSuffix "-" -}}
{{- else -}}
{{- $name := default .Chart.Name .Values.nameOverride -}}
{{- if contains $name .Release.Name -}}
{{- .Release.Name | trunc 63 | trimSuffix "-" -}}
{{- else -}}
{{- printf "%s-%s" .Release.Name $name | trunc 63 | trimSuffix "-" -}}
{{- end -}}
{{- end -}}
{{- end -}}

{{/*
Create chart name and version as used by the chart label.
*/}}
{{- define "nginx-test.chart" -}}
{{- printf "%s-%s" .Chart.Name .Chart.Version | replace "+" "_" | trunc 63 | trimSuffix "-" -}}
{{- end -}}
```

该模板定义了 "nginx-test.name"、"nginx-test.fullname"、"nginx-test.chart" 等可重用模板部分，当模板引擎读取该文件时，它存储对 nginx-test.name等的引用， 直到调用 template "nginx-test.name" 为止。然后把值渲染到模板中。

注意 {{ template "nginx-test.chart" . }} 后面有个dot(.)，这是因为一个已命名的模板（用于创建 define) 被渲染时，它将接收由该 template 调用传入的范围（scope)。没有范围传入，在模板中无法访问任何内容，因此在：

```yaml
{{- define "nginx-test.chart" -}}
这里面的 .Chart 将无法访问，导致在模板中无法看到内容，因为这里值为空
{{- end -}}
```

因此在模板中将 范围(scope) 传入即可正常使用：

```yaml
# nginx-test/templates/service.yaml
apiVersion: v1
kind: Service
metadata:
  name: {{ template "nginx-test.fullname" . }}
  在末尾传递了 . 这样就可以使用 .Values 或者 .Chart 或其它范围(scope)
```

5、Chart 依赖（requirements.yaml)：比如 WordPress Chart 依赖于 mariadb Chart， 下面是 WordPress 的依赖(requirements.yaml)：

```yaml
dependencies:
- name: mariadb
  version: 5.x.x
  repository: https://kubernetes-charts.storage.googleapis.com/
  condition: mariadb.enabled
  tags:
    - wordpress-database
```

该文件列举当前 Chart 所有的 依赖（subchart)。有几个字段是必要的：

- name: 依赖 Chart 的名称（必要）
- version: 依赖 Chart 的版本号（必要）
- repository: 依赖 Chart 的存储库完整URL，必须通过 helm repo add 添加 repository（存储库）到本地







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
