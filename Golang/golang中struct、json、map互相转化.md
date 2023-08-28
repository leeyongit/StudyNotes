# golang中struct、json、map互相转化

## 一、Json和struct互换

### （1）Json转struct例子：

```go
package main

import (
    "fmt"
    "encoding/json"
)

type People struct {
    Name string `json:"name_title"`
    Age int `json:"age_size"`
}

func JsonToStructDemo(){
    jsonStr := `
    {
      "name_title": "jqw"
      "age_size":12
    }
    `
    var people People
    json.Unmarshal([]byte(jsonStr), &people)
    fmt.Println(people)
}

func main(){
    JsonToStructDemo()
}
```

输出：`{ 0}`

注意json里面的key和struct里面的key要一致，struct中的key的首字母必须大写，而json中大小写都可以。

### （2）struct转json

​    在结构体中引入tag标签，这样匹配的时候json串对应的字段名需要与tag标签中定义的字段名匹配，当然tag中定义的名称不需要首字母大写，且对应的json串中字段名仍然大小写不敏感。此时，结构体中对应的字段名可以不用和匹配的一致，但是首字母必须大写，只有大写才是可对外提供访问的。

**例子：**

```go
package main

import (
	"fmt"
  "encoding/json"
)

type People struct {
    Name string `json:"name_title"`
    Age int `json:"age_size"`
}

func StructToJsonDemo(){
    p := People{
      Name: "jqw",
      Age: 18,
    }

    jsonBytes, err := json.Marshal(p)

    if err != nil {
            fmt.Println(err)
    }
    fmt.Println(string(jsonBytes))
}

func main(){
    StructToJsonDemo()
}
```

输出：`{"name_title":"jaw", "age_size":18}`

## **二、json和map互转**

### （1）json转map例子

```go
func JsonToMapDemo(){
    jsonStr := `
    {
        "name": "jqw",
        "age": 18
    }`

    var mapResult map[string]interface{}
    err := json.Unmarshal([]byte(jsonStr), &mapResult)
    if err != nil {
            fmt.Println("JsonToMapDemo err: ", err)
    }
    fmt.Println(mapResult)
}
```

输出：`map[name:jqw age:18]`

### （2）map转Json例子

```go
func MapToJsonDemo1(){
    mapInstances := []map[string]interface{}{}
    instance_1 := map[string]interface{}{"name": "John", "age": 10}
    instance_2 := map[string]interface{}{"name": "Alex", "age": 12}
    mapInstances = append(mapInstances, instance_1, instance_2)
    jsonStr, err := json.Marshal(mapInstances)
    if err != nil {
       fmt.Println("MapToJsonDemo err: ", err)
    }
    fmt.Println(string(jsonStr))
}
```

输出：`[{"age":10,"name":"John"},{"age":12,"name":"Alex"}]`

**例2：**

```go
func MapToJsonDemo2(){
    b, _ := json.Marshal(map[string]int{"test":1, "try":2})
    fmt.Println(string(b))
}
```

输出：`{"test":1, "try":2}`

## 三、map和struct互转

### （1）map转struct

需要安装一个第三方库

在命令行中运行： go get github.com/goinggo/mapstructure

例子：

```go
func MapToStructDemo(){
    mapInstance := make(map[string]interface{})
    mapInstance["Name"] = "jqw"
    mapInstance["Age"] = 18
    var people People
    err := mapstructure.Decode(mapInstance, &people)
    if err != nil {
        fmt.Println(err)
    }
    fmt.Println(people)
}
```

输出: `{jaw 18}`

### （2）struct转map例子

```go
func StructToMapDemo(obj interface{}) map[string]interface{}{
        obj1 := reflect.TypeOf(obj)
        obj2 := reflect.ValueOf(obj)
        var data = make(map[string]interface{})
        for i := 0; i < obj1.NumField(); i++ {
                data[obj1.Field(i).Name] = obj2.Field(i).Interface()
        }
        return data
}
func TestStructToMap(){
    student := Student{10, "jqw", 18}
    data := StructToMapDemo(student)
    fmt.Println(data)
}
```