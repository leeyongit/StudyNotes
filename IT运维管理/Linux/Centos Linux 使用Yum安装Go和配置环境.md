# Centos Linux 使用Yum安装Go和配置环境

### 查看yum的Golang

查看golang

```sh
$ yum info golang
Loading mirror speeds from cached hostfile
Installed Packages
Name        : golang
Arch        : x86_64
Version     : 1.8.3
Release     : 1.el7
Size        : 11 M
Repo        : installed
From repo   : os
Summary     : The Go Programming Language
URL         : http://golang.org/
License     : BSD and Public Domain
Description : The Go Programming Language.
```

如果没有安装，执行安装命令：

```sh
yum install golang
```

这样安装之后通过命令行输入go就可以看到相关的信息。输入`go env`查看环境信息：

### 环境配置

1）查看是否存在.bash_profile, 如果不存在则新建.bash_profile文件

```undefined
vi /etc/profile
```

2）添加环境变量 在文件后面追加如下文本：

```bash
# GOROOT
export GOROOT=/usr/lib/golang
# GOPATH
export GOPATH=/root/Work/programmer/go/gopath/
# GOPATH bin
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

需要立即生效，在终端执行如下命令：

```bash
source /etc/profile
```



