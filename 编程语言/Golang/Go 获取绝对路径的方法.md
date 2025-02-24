# Go 获取绝对路径的方法

```go
package main

import (
    "fmt"
    "os"
    "path/filepath"
    "runtime"
)

func main() {
    // 方法1: 获取当前工作目录
    workDir, err := os.Getwd()
    if err != nil {
        panic(err)
    }
    fmt.Printf("工作目录: %s\n", workDir)

    // 方法2: 获取可执行文件所在目录
    execPath, err := os.Executable()
    if err != nil {
        panic(err)
    }
    execDir := filepath.Dir(execPath)
    fmt.Printf("可执行文件目录: %s\n", execDir)

    // 方法3: 获取源代码文件所在目录
    _, filename, _, ok := runtime.Caller(0)
    if !ok {
        panic("无法获取源文件路径")
    }
    sourceDir := filepath.Dir(filename)
    fmt.Printf("源代码目录: %s\n", sourceDir)

    // 相对路径转绝对路径
    relPath := "./config/app.yaml"
    absPath, err := filepath.Abs(relPath)
    if err != nil {
        panic(err)
    }
    fmt.Printf("绝对路径: %s\n", absPath)

    // 规范化路径
    messyPath := "dir1/../dir2/./dir3//file.txt"
    cleanPath := filepath.Clean(messyPath)
    fmt.Printf("规范化路径: %s\n", cleanPath)

    // 拼接路径
    fullPath := filepath.Join(workDir, "config", "app.yaml")
    fmt.Printf("拼接路径: %s\n", fullPath)
}
```

## 使用说明

1. `os.Getwd()`: 获取当前工作目录，返回程序执行时的目录
2. `os.Executable()`: 获取可执行文件的路径，适用于打包后的程序
3. `runtime.Caller(0)`: 获取当前源代码文件的路径
4. `filepath.Abs()`: 将相对路径转换为绝对路径
5. `filepath.Clean()`: 规范化路径字符串，移除多余的分隔符和相对路径符号
6. `filepath.Join()`: 安全地拼接路径，自动处理不同操作系统的路径分隔符

## 最佳实践

1. 使用 `filepath` 包而不是直接字符串拼接
2. 总是检查错误返回值
3. 考虑跨平台兼容性，使用 `filepath.Separator` 或 `filepath.Join`
4. 对于配置文件路径，建议使用相对于可执行文件或工作目录的路径