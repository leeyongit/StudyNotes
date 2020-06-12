# Golang中 struct{} 和 struct{}{}

struct是Go中的关键字，用于定义结构类型。
例如：
```go
type User struct {
    Name string
    Age  int
}
```
**struct {}**
struct {}是一个无元素的结构体类型，通常在没有信息存储时使用。优点是大小为0，不需要内存来存储struct {}类型的值。

**struct {} {}**
struct {} {}是一个复合字面量，它构造了一个struct {}类型的值，该值也是空。

例子
```go
var set map[string]struct{}
// Initialize the set
set = make(map[string]struct{})

// Add some values to the set:
set["red"] = struct{}{}
set["blue"] = struct{}{}

// Check if a value is in the map:
_, ok := set["red"]
fmt.Println("Is red in the map?", ok)
_, ok = set["green"]
fmt.Println("Is green in the map?", ok)
```
输出内容

    Is red in the map? true
    Is green in the map? false