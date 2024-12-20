# golang 遇到的坑

## Golang 使用 JSON unmarshal() 序列化 int 被转成 float64 类型

使用 Golang 解析 JSON 格式数据时，若以 interface{} 接收数字成员，则会按照下列规则进行解析，可见

使用 Golang 对 JSON 结构进行解析（unmarshal）时，JSON 结构中的数字会被解析为 float64 类型：



```go
    bool, for JSON booleans
 
    float64, for JSON numbers
 
    string, for JSON strings
 
    []interface{}, for JSON arrays
 
    map[string]interface{}, for JSON objects
 
    nil for JSON null
```

如果要转换为整型，可用强制类型转换：

 int( a["id"].(float64) )  // 将 interface{} 类型的 “id” 键申明为 float64 类型，再转换为 int 型