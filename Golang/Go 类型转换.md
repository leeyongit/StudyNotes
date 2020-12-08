# Go 类型转换

### int，int32，int64相互转换

int转换成int32，int64

```go
// int转换成int32
i32 = int32(i)
// int转换成int64
i64 = int64(i)
```

int32，int64转换成int

```go
// int32转换成int
i = int(int32)
// int64转换成int
i = int(int64)
```

# int -> string

int，int32，int64转换成string

```go
// 通过fmt.Sprintf方法转换（%d代表Integer，i可以是int，int32，int64类型）
str1 := fmt.Sprintf("%d", i)
// 通过strconv.Itoa方法转换（i是int类型）
str2 := strconv.Itoa(i)
// 通过strconv.FormatInt方法转换（i可以是int，int32，int64类型）
str3 := strconv.FormatInt(int64(i), 10)
```

# string -> int

```go
strInt, err := strconv.Atoi(str) 							// string转换成int
strInt64, _ := strconv.ParseInt(str, 10, 64) 	// string转换成int64
strInt32, _ := strconv.ParseInt(str, 10, 32) 	// string转换成int32
// 这里strInt32实际上还是int64类型的，只是截取了32位，所以最终还是要强转一下变成int32类型，如果不强转成int32是会编译报错的
var realInt32 int32 = 0
realInt32 := int32(strInt32)

```

# string -> float

- 使用方法：`func ParseFloat(s string, bitSize int) (f float64, err error)`
  bitSize：32或64 对应系统的位数

```go
strF := "250.56"
str, err := strconv.ParseFloat(strF, 64)
if err != nil {
    fmt.Println(err)
}
fmt.Printf("type:%T value:%#v\n", str, str)
```

# float -> string

- 使用方法：`func FormatFloat(f float64, fmt byte, prec, bitSize int) string`
  bitSize表示f的来源类型（32：float32、64：float64），会据此进行舍入。

> fmt表示格式：'f'（-ddd.dddd）、'b'（-ddddp±ddd，指数为二进制）、'e'（-d.dddde±dd，十进制指数）、'E'（-d.ddddE±dd，十进制指数）、'g'（指数很大时用'e'格式，否则’f’格式）、'G'（指数很大时用’E’格式，否则'f'格式）。
>
> prec控制精度（排除指数部分）：对'f'、'e'、'E'，它表示小数点后的数字个数；对'g'、'G'，它控制总的数字个数。如果prec 为-1，则代表使用最少数量的、但又必需的数字来表示f。

- 测试代码：

```
num := 250.56
str := strconv.FormatFloat(num, 'f', 4,64)
fmt.Printf("type:%T value:%#v\n", str, str)
```

输出：type:string value:"250.5600"

当然，以上类型转string的话，可以直接用`fmt.Sprintf`实现。
举个例子：

```
num := 250.56
str := fmt.Sprintf("%.2f", num)
fmt.Printf("type:%T value:%#v\n", str, str)
```