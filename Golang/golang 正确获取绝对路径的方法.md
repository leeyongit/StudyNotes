# golang 正确获取绝对路径的方法

Golang编译出来之后是独立的可执行程序，路径的问题经常让人头疼，正确获取绝对路径非常重要， 方法如下：
```go
package main

import (
    "fmt"
    "os"
    "os/exec"
    "path/filepath"
    "strings"
)

func main() {
    fmt.Println("GoLang 获取程序运行绝对路径")
    fmt.Println(GetCurrPath())
}

func GetCurrPath() string {
    file, _ := exec.LookPath(os.Args[0])
    path, _ := filepath.Abs(file)
    index := strings.LastIndex(path, string(os.PathSeparator))
    ret := path[:index]
    return ret
}
```
如果你在本地运行go run you.go，则输出：

GoLang 获取程序运行绝对路径
> /tmp/go-build183214745/command-line-arguments/_obj/exe

这是go 编译文件时生成的一个路径。不过很多时候需要读取配置，由于执行目录有时候不在程序所在目录。
新的方式获取：
```go
var abPath string
_, filename, _, ok := runtime.Caller(1)
if ok {
    abPath = path.Dir(filename)
}
```