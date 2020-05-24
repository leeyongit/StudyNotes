#微服务框架---搭建 go-micro环境

## 1.安装micro

需要使用GO1.11以上版本

```sh
#linux 下
export GO111MODULE=onexport GOPROXY=https://goproxy.io
# windows下设置如下环境变量
setx GO111MODULE on
setx GOPROXY https://goproxy.io
# 使用如下指令安装
go get -u -v github.com/micro/microgo
get -u -v github.com/micro/go-micro
```

## 2.安装 protoc

```
https://github.com/protocolbuffers/protobuf/releases
//下载,不同的版本文件名称不一样,我们这里选择protoc-3.9.1-win64.zipprotoc-3.9.1-win64.zip
//解压到目标文件架,我们以e:\dev为例e:\dev\protoc-3.9.1-win64
//添加e:\dev\protoc-3.9.1-win64\bin到环境变量path
```

## 3.安装protoc-gen-micro插件

```sh
//这个插件主要作用是通过.proto文件生成适用于go-micro的代码
go get -u -v github.com/micro/protoc-gen-micro
```

## 4.安装 consul
```
brew install consul
consul agent -dev
consul members
curl localhost:8500/v1/catalog/nodes
```


## 5.创建微服务 hello world

```sh
//使用如下指令创建微服务
>micro new techidea8.com/microapp/hello
Creating service go.micro.srv.hello in E:\winlion\gopath\src\techidea8.com\microapp\hello
.
├── main.go
├── plugin.go
├── handler
│   └── hello.go
├── subscriber
│   └── hello.go
├── proto\hello
│   └── hello.proto
├── Dockerfile
├── Makefile
├── README.md
└── go.mod
download protobuf for micro:
brew install protobuf
go get -u github.com/golang/protobuf/{proto,protoc-gen-go}
go get -u github.com/micro/protoc-gen-micro
compile the proto file hello.proto:
cd E:\winlion\gopath\src\techidea8.com\microapp\hello
protoc --proto_path=.:$GOPATH/src --go_out=. --micro_out=. proto/hello/hello.proto
```

## 6.生成适配proto的golang代码

```sh
#切换到项目目录下
>cd /d E:\winlion\gopath\src\techidea8.com\microapp\hello
# 根据proto生成文件
>protoc --proto_path=. --go_out=. --micro_out=. proto/hello/hello.proto
# 启动应用
>go run main.go
2019/08/19 13:00:46 Transport [http] Listening on [::]:54689
2019/08/19 13:00:46 Broker [http] Connected to [::]:54690
2019/08/19 13:00:46 Registry [mdns] Registering node: go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26
2019/08/19 13:00:46 Subscribing go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26 to topic: go.micro.srv.hello
2019/08/19 13:00:46 Subscribing go.micro.srv.hello-4851dce2-ab5d-4e4c-801e-44dae5d93f26 to topic: go.micro.srv.hello
# 查看是否启动
>micro list services
go.micro.srv.hello
topic:go.micro.srv.hello
```

## 7.启动restful api接口支持支持

注意其中的--namespace参数,我们每一个微服务都属于一个命名空间,通过api暴露出来该命名空间后,满足go.micro.srv.*格式的微服务都可以访问。如go.micro.srv.hello可以通过如下格式访问

```sh
# 访问http://127.0.0.1:8080/hello/call
>micro api --namespace=go.micro.srv
2019/08/19 13:07:11 Registering API Default Handler at /
2019/08/19 13:07:11 HTTP API Listening on [::]:8080
2019/08/19 13:07:11 Transport [http] Listening on [::]:54934
2019/08/19 13:07:11 Broker [http] Connected to [::]:54935
2019/08/19 13:07:11 Registry [mdns] Registering node: go.micro.api-1753185c-b8e1-49c4-aa0f-617f243a8e2a
```

## 8.测试