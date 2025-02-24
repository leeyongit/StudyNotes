#  Go 语言的交叉编译

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

**Mac 下编译 Linux 可执行程序**

```sh
CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go
```

**Mac 上使用Docker交叉编译Golang项目到Linux**

```sh
docker run --rm -it --network=none -v $GOPATH:/go golang:1.13 bash -c 'cd $GOPATH/src/go-nas && go build'
```



### 

