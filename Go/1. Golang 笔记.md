# Golang 学习笔记

**Mac 下 GOPATH 设定**
```sh
vim ~/.bash_profile
export GOPATH=$HOME/Desktop/workspace/golang
export GOBIN=$GOPATH/bin
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



### Go语言fmt.Sprintf（格式化输出）

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

### int，int32，int64相互转换

int转换成int32，int64

```go
// int转换成int32
i32 = int32(i)
// int转换成int64
i64 = int64(i)
```

int32，int64转换成int

```go
// int32转换成int
i = int(int32)
// int64转换成int
i = int(int64)
```

### int和string相互转换

int，int32，int64转换成string

```go
// 通过fmt.Sprintf方法转换（%d代表Integer，i可以是int，int32，int64类型）
str1 := fmt.Sprintf("%d", i)
// 通过strconv.Itoa方法转换（i是int类型）
str2 := strconv.Itoa(i)
// 通过strconv.FormatInt方法转换（i可以是int，int32，int64类型）
str3 := strconv.FormatInt(int64(i), 10)
```

fmt.Sprintf

```go
// Sprint formats using the default formats for its operands and returns the resulting string.
// Spaces are added between operands when neither is a string.
func Sprint(a ...interface{}) string {
    p := newPrinter()
    p.doPrint(a)
    s := string(p.buf)
    p.free()
    return s
}
```

### string转换成int，int32，int64

```go
// string转换成int64
strInt64, _ := strconv.ParseInt(str, 10, 64)
// string转换成int32
strInt32, _ := strconv.ParseInt(str, 10, 32)
// 这里strInt32实际上还是int64类型的，只是截取了32位，所以最终还是要强转一下变成int32类型，如果不强转成int32是会编译报错的
var realInt32 int32 = 0
realInt32 := int32(strInt32)
// string转换成int
strInt, err := strconv.Atoi(str)
```

