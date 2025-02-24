在 Go 语言中，`struct`、`JSON` 和 `map` 之间的相互转换是非常常见的操作。以下是详细的转换方法和示例代码：

---

### 一、`struct` 与 `JSON` 的相互转换
Go 语言的标准库 `encoding/json` 提供了 `Marshal` 和 `Unmarshal` 函数，用于实现 `struct` 和 `JSON` 之间的转换。

#### 1. **`struct` 转 `JSON`**
- 使用 `json.Marshal` 将 `struct` 转换为 `JSON` 字节切片。
- 示例：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  type Person struct {
      Name string `json:"name"`
      Age  int    `json:"age"`
  }
  
  func main() {
      p := Person{Name: "Alice", Age: 25}
      jsonData, err := json.Marshal(p)
      if err != nil {
          fmt.Println("Error:", err)
          return
      }
      fmt.Println(string(jsonData)) // 输出: {"name":"Alice","age":25}
  }
  ```

#### 2. **`JSON` 转 `struct`**
- 使用 `json.Unmarshal` 将 `JSON` 字节切片转换为 `struct`。
- 示例：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  type Person struct {
      Name string `json:"name"`
      Age  int    `json:"age"`
  }
  
  func main() {
      jsonData := []byte(`{"name":"Alice","age":25}`)
      var p Person
      err := json.Unmarshal(jsonData, &p)
      if err != nil {
          fmt.Println("Error:", err)
          return
      }
      fmt.Println(p) // 输出: {Alice 25}
  }
  ```

---

### 二、`map` 与 `JSON` 的相互转换
`map` 和 `JSON` 之间的转换同样使用 `json.Marshal` 和 `json.Unmarshal`。

#### 1. **`map` 转 `JSON`**
- 示例：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  func main() {
      m := map[string]interface{}{
          "name": "Alice",
          "age":  25,
      }
      jsonData, err := json.Marshal(m)
      if err != nil {
          fmt.Println("Error:", err)
          return
      }
      fmt.Println(string(jsonData)) // 输出: {"age":25,"name":"Alice"}
  }
  ```

#### 2. **`JSON` 转 `map`**
- 示例：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  func main() {
      jsonData := []byte(`{"name":"Alice","age":25}`)
      var m map[string]interface{}
      err := json.Unmarshal(jsonData, &m)
      if err != nil {
          fmt.Println("Error:", err)
          return
      }
      fmt.Println(m) // 输出: map[age:25 name:Alice]
  }
  ```

---

### 三、`struct` 与 `map` 的相互转换
Go 语言没有直接提供 `struct` 和 `map` 之间的转换函数，但可以通过 `JSON` 作为中间格式实现转换，或者使用反射（`reflect` 包）直接操作。

#### 1. **`struct` 转 `map`**
- 通过 `JSON` 中间格式：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  type Person struct {
      Name string `json:"name"`
      Age  int    `json:"age"`
  }
  
  func main() {
      p := Person{Name: "Alice", Age: 25}
      var m map[string]interface{}
      jsonData, _ := json.Marshal(p)
      json.Unmarshal(jsonData, &m)
      fmt.Println(m) // 输出: map[age:25 name:Alice]
  }
  ```

- 使用反射（`reflect` 包）：
  ```go
  package main
  
  import (
      "fmt"
      "reflect"
  )
  
  type Person struct {
      Name string
      Age  int
  }
  
  func StructToMap(obj interface{}) map[string]interface{} {
      v := reflect.ValueOf(obj)
      if v.Kind() == reflect.Ptr {
          v = v.Elem()
      }
      if v.Kind() != reflect.Struct {
          panic("not a struct")
      }
  
      m := make(map[string]interface{})
      t := v.Type()
      for i := 0; i < v.NumField(); i++ {
          field := t.Field(i)
          value := v.Field(i).Interface()
          m[field.Name] = value
      }
      return m
  }
  
  func main() {
      p := Person{Name: "Alice", Age: 25}
      m := StructToMap(p)
      fmt.Println(m) // 输出: map[Name:Alice Age:25]
  }
  ```

#### 2. **`map` 转 `struct`**
- 通过 `JSON` 中间格式：
  ```go
  package main
  
  import (
      "encoding/json"
      "fmt"
  )
  
  type Person struct {
      Name string `json:"name"`
      Age  int    `json:"age"`
  }
  
  func main() {
      m := map[string]interface{}{
          "name": "Alice",
          "age":  25,
      }
      jsonData, _ := json.Marshal(m)
      var p Person
      json.Unmarshal(jsonData, &p)
      fmt.Println(p) // 输出: {Alice 25}
  }
  ```

- 使用反射（`reflect` 包）：
  ```go
  package main
  
  import (
      "fmt"
      "reflect"
  )
  
  type Person struct {
      Name string
      Age  int
  }
  
  func MapToStruct(m map[string]interface{}, obj interface{}) {
      v := reflect.ValueOf(obj)
      if v.Kind() != reflect.Ptr || v.Elem().Kind() != reflect.Struct {
          panic("not a pointer to struct")
      }
      v = v.Elem()
      t := v.Type()
      for i := 0; i < v.NumField(); i++ {
          field := t.Field(i)
          if value, ok := m[field.Name]; ok {
              v.Field(i).Set(reflect.ValueOf(value))
          }
      }
  }
  
  func main() {
      m := map[string]interface{}{
          "Name": "Alice",
          "Age":  25,
      }
      var p Person
      MapToStruct(m, &p)
      fmt.Println(p) // 输出: {Alice 25}
  }
  ```

---

### 四、总结
| 转换类型           | 方法                        | 示例代码                  |
| ------------------ | --------------------------- | ------------------------- |
| `struct` 转 `JSON` | `json.Marshal`              | [示例](#1-struct-转-json) |
| `JSON` 转 `struct` | `json.Unmarshal`            | [示例](#2-json-转-struct) |
| `map` 转 `JSON`    | `json.Marshal`              | [示例](#1-map-转-json)    |
| `JSON` 转 `map`    | `json.Unmarshal`            | [示例](#2-json-转-map)    |
| `struct` 转 `map`  | 通过 `JSON` 或 `reflect` 包 | [示例](#1-struct-转-map)  |
| `map` 转 `struct`  | 通过 `JSON` 或 `reflect` 包 | [示例](#2-map-转-struct)  |

通过以上方法，可以灵活地在 `struct`、`JSON` 和 `map` 之间进行转换，满足不同的开发需求。