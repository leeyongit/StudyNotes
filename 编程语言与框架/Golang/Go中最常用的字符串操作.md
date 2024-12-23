# Go String Operations Guide

## 基本操作

```go
// 字符串声明
str := "Hello, 世界"
str2 := `多行
字符串`

// 获取长度
length := len(str)               // 字节长度
runeCount := utf8.RuneCountInString(str)  // 字符数

// 字符串拼接
s1 := "Hello" + " " + "World"    // 使用 + 操作符
s2 := fmt.Sprintf("%s %s", "Hello", "World")  // 格式化
s3 := strings.Join([]string{"Hello", "World"}, " ")  // Join方法
```

## 字符串修改

```go
// 替换
s := strings.Replace("hello hello", "l", "L", 2)  // heLLo hello
all := strings.ReplaceAll("hello hello", "l", "L")  // heLLo heLLo

// 转换大小写
upper := strings.ToUpper("hello")  // HELLO
lower := strings.ToLower("HELLO")  // hello
title := strings.Title("hello world")  // Hello World

// 去除空格
trimmed := strings.TrimSpace(" hello ")  // "hello"
trimLeft := strings.TrimLeft("!!!hello", "!")  // "hello"
trimRight := strings.TrimRight("hello!!!", "!")  // "hello"
```

## 字符串查找

```go
// 包含关系
contains := strings.Contains("hello", "ll")  // true
prefix := strings.HasPrefix("hello", "he")   // true
suffix := strings.HasSuffix("hello", "lo")   // true

// 查找位置
index := strings.Index("hello", "l")      // 2
lastIndex := strings.LastIndex("hello", "l")  // 3

// 计数
count := strings.Count("hello", "l")  // 2
```

## 分割与合并

```go
// 分割
parts := strings.Split("a,b,c", ",")    // ["a", "b", "c"]
lines := strings.Split("a\nb\nc", "\n") // ["a", "b", "c"]

// 限制分割数量
parts = strings.SplitN("a,b,c", ",", 2) // ["a", "b,c"]

// 合并
joined := strings.Join([]string{"a", "b", "c"}, ",") // "a,b,c"
```

## 转换

```go
// 字符串与数字转换
i, err := strconv.Atoi("123")     // 字符串到整数
s := strconv.Itoa(123)            // 整数到字符串

f, err := strconv.ParseFloat("123.45", 64)  // 字符串到浮点数
b, err := strconv.ParseBool("true")         // 字符串到布尔值

// 字符串与字节切片转换
bytes := []byte("hello")          // 字符串到字节切片
str := string(bytes)              // 字节切片到字符串
```

## 字符串比较

```go
// 直接比较
equal := "hello" == "hello"  // true

// 忽略大小写比较
equalFold := strings.EqualFold("Hello", "hello")  // true

// 比较大小
comp := strings.Compare("a", "b")  // -1, 0, 或 1
```

## 字符串迭代

```go
// 按字节迭代
str := "Hello, 世界"
for i := 0; i < len(str); i++ {
    fmt.Printf("%x ", str[i])
}

// 按字符（rune）迭代
for _, ch := range str {
    fmt.Printf("%c ", ch)
}

// 使用strings.Builder构建字符串
var builder strings.Builder
builder.WriteString("Hello")
builder.WriteString(" ")
builder.WriteString("World")
result := builder.String()
```

## 常用包

```go
import (
    "strings"     // 字符串操作
    "strconv"     // 字符串转换
    "unicode"     // Unicode相关
    "unicode/utf8" // UTF-8编码
    "fmt"         // 格式化
    "bytes"       // 字节操作
)
```



## 中文字符处理

```go
// 获取字符串中的中文字符数
func getChineseCharCount(s string) int {
    count := 0
    for _, r := range s {
        if unicode.Is(unicode.Han, r) {
            count++
        }
    }
    return count
}

// 获取字符串真实长度（ASCII字符算1个，中文字符算2个）
func getRealLength(s string) int {
    length := 0
    for _, r := range s {
        if unicode.Is(unicode.Han, r) {
            length += 2
        } else {
            length++
        }
    }
    return length
}

// 示例使用
func example() {
    str := "Hello世界golang测试"
    
    fmt.Printf("字节长度: %d\n", len(str))                    // 字节长度
    fmt.Printf("Unicode字符数: %d\n", utf8.RuneCountInString(str)) // Unicode字符总数
    fmt.Printf("中文字符数: %d\n", getChineseCharCount(str))       // 仅中文字符数
    fmt.Printf("显示长度: %d\n", getRealLength(str))              // 考虑中文显示宽度的长度
}
```

