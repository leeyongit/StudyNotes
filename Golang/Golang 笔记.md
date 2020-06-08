# Golang 学习笔记

**Mac下配置.bash_profile（环境变量）**

```sh
vim ~/.bash_profile
export GOPATH=$HOME/Desktop/workspace/golang
export GOBIN=$GOPATH/bin
export PATH=$PATH:$GOPATH/bin
export GOPROXY=https://goproxy.io
source ~/.bash_profile
```

**安装交叉编译工具链编译windows和linux下的C/C++**

```sh
brew install FiloSottile/musl-cross/musl-cross
brew install mingw-w64
```

**Mac 下编译windows下应用**
```sh
env GOOS="windows" GOARCH="386" CGO_ENABLED="1" CC="i686-w64-mingw32-gcc"   go build main.go
# or to target win 64
env GOOS="windows" GOARCH="amd64" CGO_ENABLED="1" CC="x86_64-w64-mingw32-gcc" go build main.go
```

**golang编译程序从后台运行，不出现dos窗口**
```sh
go build -ldflags "-H windowsgui"
```

**Mac 上交叉编译Golang项目到Linux**
```sh
docker run --rm -it --network=none -v $GOPATH:/go golang:1.13 bash -c 'cd $GOPATH/src/go-nas && go build'
```

**VS Code 调试错误**
could not launch process: executables built by Go 1.11 or later need Delve built by Go 1.11 or later

```go
go get -u github.com/go-delve/delve/cmd/dlv
```

## Go语言fmt.Sprintf（格式化输出）

```go
fmt.Sprintf(格式化样式, 参数列表…)
```

| 动  词  |          功  能                          |
| ------ | ---------------------------------------- |
| %v     | 按值的本来值输出                         |
| %+v    | 在 %v 基础上，对结构体字段名和值进行展开 |
| %#v    | 输出 Go 语言语法格式的值                 |
| %T     | 输出 Go 语言语法格式的类型和值           |
| %%     | 输出 % 本体                              |
| %b     | 整型以二进制方式显示                     |
| %o     | 整型以八进制方式显示                     |
| %d     | 整型以十进制方式显示                     |
| %x     | 整型以十六进制方式显示                   |
| %X     | 整型以十六进制、字母大写方式显示         |
| %U     | Unicode 字符                             |
| %f     | 浮点数                                   |
| %p     |                                          |

