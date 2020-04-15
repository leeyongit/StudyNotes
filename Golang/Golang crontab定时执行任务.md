Golang crontab定时任务
========================

```go
package main

import (
    l4g "github.com/alecthomas/log4go"
    "github.com/robfig/cron"
)

func text() {
    l4g.Info("text")
}

func main() {
    c := cron.New()
    c.AddFunc("* * * * * *", func() { text() })
    c.Start()
    select {}
}
```

首先cron.New()一个实例，然后添加定时任务AddFunc(),他接受两个函数第一个是时间，第二个是函数。我们使用*表示每一秒，每一分…..
上例中 因为传入的是**，所以他就会每一秒钟执行一次text。
```go
f1 f2 f3 f4 f5 f6 program
```
其中 f1 是表示秒，f2表示分钟，f3 表示小时，f4 表示一个月份中的第几日，f5 表示月份，f6表示一个星期中的第几天。program 表示要执行的程式。

| 规则              | 说明                                   |
| ----------------- | -------------------------------------- |
| */2 * * * * *     | 每个偶数分执行text                     |
| */2 */2 * * * *   | 每个偶数分 并且是偶数秒执行text        |
| 2,4,6 * * * * *   | 每分钟的2,4,6这三个秒执行text          |
| 2,4,6 5-6 * * * 1 | 每周一的5点2,4,6秒和6点2,4,6秒执行text |
| @daily            | 每天一次                               |
| @midnight         | 同上                                   |
| @every 1m30s      | 定时1分30秒执行text                    |
