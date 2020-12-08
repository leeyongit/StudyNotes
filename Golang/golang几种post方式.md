# golang几种post方式

**1.普通的post\get请求**

```go
　　 var r http.Request
    r.ParseForm()
    r.Form.Add("uuid", orderUUID)
    bodystr := strings.TrimSpace(r.Form.Encode())
    request, err := http.NewRequest("GET", url, strings.NewReader(bodystr))
    if err != nil {
     　　//TODO:  
    }
    request.Header.Set("Content-Type", "application/x-www-form-urlencoded")
    request.Header.Set("Connection", "Keep-Alive")

    var resp *http.Response
    resp, err = http.DefaultClient.Do(request)
    if err != nil {
　　　　//TODO:
    }
```

 

**2. body全部二进制数据流进行post**

```go
//body提交二进制数据
func DoBytesPost(url string, data []byte) ([]byte, error) {

    body := bytes.NewReader(data)
    request, err := http.NewRequest(POST_METHOD, url, body)
    if err != nil {
        log.Println("http.NewRequest,[err=%s][url=%s]", err, url)
        return []byte(""), err
    }
    request.Header.Set("Connection", "Keep-Alive")
    var resp *http.Response
    resp, err = http.DefaultClient.Do(request)
    if err != nil {
        log.Println("http.Do failed,[err=%s][url=%s]", err, url)
        return []byte(""), err
    }
    defer resp.Body.Close()
    b, err := ioutil.ReadAll(resp.Body)
    if err != nil {
        log.Println("http.Do failed,[err=%s][url=%s]", err, url)
    }
    return b, err
}
```

 

**3. 模拟web表单文件上传进行post**

```go
func newfileUploadRequest(uri string, params map[string]string, paramName, path string) (*http.Request, error) {
    file, err := os.Open(path)
    if err != nil {
        return nil, err
    }
    defer file.Close()

    body := &bytes.Buffer{}
    writer := multipart.NewWriter(body)
    part, err := writer.CreateFormFile(paramName, path)
    if err != nil {
        return nil, err
    }
    _, err = io.Copy(part, file)

    for key, val := range params {
        _ = writer.WriteField(key, val)
    }
    err = writer.Close()
    if err != nil {
        return nil, err
    }
    request, err := http.NewRequest("POST", uri, body)
    request.Header.Set("Content-Type", writer.FormDataContentType())
    return request, err
}
```

# golang 模擬 curl post

```plain
package main

import (
	// "bytes"
	"fmt"
	"io/ioutil"
	"net/http"
	"net/url"
	// "os"
	// "strings"
)

func main() {
	client := &http.Client{}
	//向服務端傳送get請求
	//request, _ := http.NewRequest("GET", "http://192.168.1.35:9091/?publicKey=&privateKey=&info=sad&message=", nil)
	//response, _ := http.PostForm("http://192.168.1.35:9091", url.Values{"publicKey": {"-----BEGIN+PUBLIC+KEY-----%0D%0AMIGfMA0GCSqGSIb3DQEBAQUAA4GNADCBiQKBgQDZsfv1qscqYdy4vY%2BP4e3cAtmv%0D%0AppXQcRvrF1cB4drkv0haU24Y7m5qYtT52Kr539RdbKKdLAM6s20lWy7%2B5C0Dgacd%0D%0AwYWd%2F7PeCELyEipZJL07Vro7Ate8Bfjya%2BwltGK9%2BXNUIHiumUKULW4KDx21%2B1NL%0D%0AAUeJ6PeW%2BDAkmJWF6QIDAQAB%0D%0A-----END+PUBLIC+KEY-----"}, "privateKey": {""}, "info": {"sad"}, "message": {""}})
	//接收服務端返回給客戶端的資訊
	// response, _ := client.Do(request)
	// if response.StatusCode == 200 {
	// 	str, _ := ioutil.ReadAll(response.Body)
	// 	bodystr := string(str)
	// 	fmt.Println(bodystr)
	// }

	//post請求
	postValues := url.Values{}
	postValues.Add("publicKey", "")
	postValues.Add("privateKey", `----nGDd4/mujoJBr5mkrw
DPwqA3N5TMNDQVGv8gMCQQCaKGJgWYgvo3/milFfImbp+m7/Y3vCptarldXrYQWO
AQjxwc71ZGBFDITYvdgJM1MTqc8xQek1FXn1vfpy2c6O
-----END RSA PRIVATE KEY-----`)
	postValues.Add("info", "")
	postValues.Add("message", `DkCxcs0z6Z03uHWOHOASf2xen+7oNoSad+KG2ss0hkE79211GlgjepmMFRW4zLiF51pVYHHOBFDYYJrnokq5d0ceKYY6ONzbBYKCJMzD7guN3qMYf48Cl9g0bDVb1oMbuN2PstzORe800Q72moQaHVRPiqh7VZ6NCXnkLrtnY64=`)

	resp, err := client.PostForm("http://xxx.xxx.1.xxx:9091", postValues)
	defer resp.Body.Close()
	if err != nil {
		fmt.Println(err.Error())
	}
	if resp.StatusCode == 200 {
		body, _ := ioutil.ReadAll(resp.Body)
		fmt.Println(string(body))
	}
}
```