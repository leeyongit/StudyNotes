## json数据解码的两种方法NewDecoder与Unmarshal

golang中处理http响应数据解码，一般有两种方式

- json.Unmarshal进行解码

```go
func HandleUse(w http.ResponseWriter, r *http.Request) {
    var u Use
    data, err := ioutil.ReadAll(r.Body)
    if err != nil {
        w.WriteHeader(http.StatusBadRequest)
        return
    }
    if err := json.Unmarshal(data, &u); err != nil {
        w.WriteHeader(http.StatusInternalServerError)
        return
    }
    w.WriteHeader(http.StatusOK)
    fmt.Fprintf(w, "姓名：%s，年龄：%d", u.Name, u.Age)

}
```

- json.NewDecoder解码

```go
func HandleUse(w http.ResponseWriter, r *http.Request) {
    var u Use
    if err := json.NewDecoder(r.Body).Decode(&u); err != nil {
        w.WriteHeader(http.StatusInternalServerError)
        return
    }
    w.WriteHeader(http.StatusOK)
    fmt.Fprintf(w, "姓名：%s，年龄：%d", u.Name, u.Age)

}
```

### 区别

1. json.NewDecoder是从一个流里面直接进行解码，代码精干
2. json.Unmarshal是从已存在与内存中的json进行解码
3. 相对于解码，json.NewEncoder进行大JSON的编码比json.marshal性能高，因为内部使用pool

### 场景应用

1. json.NewDecoder用于http连接与socket连接的读取与写入，或者文件读取
2. json.Unmarshal用于直接是byte的输入