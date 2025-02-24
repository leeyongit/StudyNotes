# Go HTTP Request Examples

## 1. Basic GET/POST Requests

```go
// GET 请求
func makeGetRequest() {
    resp, err := http.Get("http://example.com/api")
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
    
    body, err := io.ReadAll(resp.Body)
    if err != nil {
        log.Fatal(err)
    }
    fmt.Printf("Response: %s\n", body)
}

// POST 请求
func makePostRequest() {
    data := map[string]string{
        "name": "test",
        "value": "123",
    }
    jsonData, err := json.Marshal(data)
    if err != nil {
        log.Fatal(err)
    }
    
    resp, err := http.Post("http://example.com/api",
        "application/json",
        bytes.NewBuffer(jsonData))
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
}
```

## 2. Binary Data POST

```go
func postBinaryData() {
    // 读取二进制文件
    data, err := os.ReadFile("file.bin")
    if err != nil {
        log.Fatal(err)
    }

    // 创建请求
    req, err := http.NewRequest("POST", "http://example.com/upload",
        bytes.NewReader(data))
    if err != nil {
        log.Fatal(err)
    }
    
    // 设置头部
    req.Header.Set("Content-Type", "application/octet-stream")
    
    // 发送请求
    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
}
```

## 3. Multipart Form File Upload

```go
func uploadFile() {
    // 创建buffer和writer
    var buff bytes.Buffer
    writer := multipart.NewWriter(&buff)
    
    // 创建文件表单字段
    fileWriter, err := writer.CreateFormFile("file", "test.txt")
    if err != nil {
        log.Fatal(err)
    }
    
    // 打开文件
    file, err := os.Open("test.txt")
    if err != nil {
        log.Fatal(err)
    }
    defer file.Close()
    
    // 复制文件内容
    _, err = io.Copy(fileWriter, file)
    if err != nil {
        log.Fatal(err)
    }
    
    // 添加其他表单字段
    writer.WriteField("name", "test")
    writer.Close()
    
    // 发送请求
    resp, err := http.Post("http://example.com/upload",
        writer.FormDataContentType(),
        &buff)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
}
```

## 4. CURL-like POST

```go
func curlStylePost() {
    // 构造请求数据
    data := url.Values{}
    data.Set("name", "test")
    data.Set("value", "123")
    
    // 创建请求
    req, err := http.NewRequest("POST", "http://example.com/api",
        strings.NewReader(data.Encode()))
    if err != nil {
        log.Fatal(err)
    }
    
    // 设置头部，模拟curl
    req.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    req.Header.Set("User-Agent", "curl/7.64.1")
    
    // 发送请求
    client := &http.Client{}
    resp, err := client.Do(req)
    if err != nil {
        log.Fatal(err)
    }
    defer resp.Body.Close()
}
```

每个示例都包含了必要的错误处理和资源清理（使用 defer）。使用时需要导入相应的包：

```go
import (
    "bytes"
    "encoding/json"
    "fmt"
    "io"
    "log"
    "mime/multipart"
    "net/http"
    "net/url"
    "os"
    "strings"
)
```