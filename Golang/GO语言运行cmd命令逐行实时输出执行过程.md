# GO语言运行cmd命令逐行实时输出执行过程

Golang运行cmd命令逐行实时输出执行过程，注意，不是一次输出所有的内容，而是按照执行的过程，逐行逐行的实时显示出来
代码如下：
```go
package main

import (
    "bufio"
    "fmt"
    "io"
    "os/exec"
)

func main() {
    command := "ls"
    params := []string{"-l"}
    //执行cmd命令: ls -l
    execCommand(command, params)
}

func execCommand(commandName string, params []string) bool {
    cmd := exec.Command(commandName, params...)

    //显示运行的命令
    fmt.Println(cmd.Args)

    stdout, err := cmd.StdoutPipe()

    if err != nil {
        fmt.Println(err)
        return false
    }

    cmd.Start()

    reader := bufio.NewReader(stdout)

    //实时循环读取输出流中的一行内容
    for {
        line, err2 := reader.ReadString('\n')
        if err2 != nil || io.EOF == err2 {
            break
        }
        fmt.Println(line)
    }

    cmd.Wait()
    return true
}
```