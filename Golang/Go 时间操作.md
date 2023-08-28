Go 时间操作
---

**格式化当前时间**

```go
fmt.Println(time.Now().Format("2006-01-02 15:04:05"))
```
> 2014-01-07 09:42:20

**获取当前时间戳**

```go
fmt.Println(time.Now().Unix())
```
> 1389058332

**时间戳转str格式化时间**
```go
str_time := time.Unix(1389058332, 0).Format("2006-01-02 15:04:05")
fmt.Println(str_time)
```
> 2014-01-07 09:32:12

**str格式化时间转时间戳**
```go
the_time := time.Date(2014, 1, 7, 5, 50, 4, 0, time.Local)
unix_time := the_time.Unix()
fmt.Println(unix_time)
```
> 389045004

**使用time.Parse将str格式化时间转时间戳**
```go
the_time, err := time.Parse("2006-01-02 15:04:05", "2014-01-08 09:04:41")
if err == nil {
    unix_time := the_time.Unix()
    fmt.Println(unix_time)
}
```
> 1389171881

**获取当前月份第一天的日期的需求，也就是如果今天的日期是"20170110"，我需要获取到的日期是"20170101"**

方式一：通过time获取当月的第一天

```go
//昨天的日期
yesterdayTime := time.Now().AddDate(0, 0, -1)
//上周的日期
lastweekTime := time.Now().AddDate(0, 0, -8)
```
缺点：无法确定今天与当月第一天间隔多少天，所以需要结合方式二一起使用

方式二：通过time分别获取当前日期的年，月，日，然后重新拼接

```go
year := time.Now().Year()
month := time.Now().Month()
day := time.Now().Day()
```
缺点：month获取出来是英文January，而不是01

方式三：将日期转换成字符串，然后截取字符串，删除字符串的最后两位字符加上"01"，或者是直接替换最后两位字符
缺点：在Go官方strings包中并没有找到合适的截取或者替换方法

### 通过系统time来获取上月的开始和结束日期

```go
package main

import (
    "fmt"
    "time"
)

const DATE_FORMAT = "2006-01-02"

func main() {
    year, month, _ := time.Now().Date()
    thisMonth := time.Date(year, month, 1, 0, 0, 0, 0, time.Local)
    start := thisMonth.AddDate(0, -1, 0).Format(DATE_FORMAT)
    end := thisMonth.AddDate(0, 0, -1).Format(DATE_FORMAT)
    timeRange := fmt.Sprintf("%s~%s", start, end)
    fmt.Println(timeRange)
}
```

GET_MACHINE_VARIABLES