# Go 类型转换

## int，int32，int64相互转换

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

## int和string相互转换

int，int32，int64转换成string

```go
// 通过fmt.Sprintf方法转换（%d代表Integer，i可以是int，int32，int64类型）
str1 := fmt.Sprintf("%d", i)
// 通过strconv.Itoa方法转换（i是int类型）
str2 := strconv.Itoa(i)
// 通过strconv.FormatInt方法转换（i可以是int，int32，int64类型）
str3 := strconv.FormatInt(int64(i), 10)
```

fmt.Sprintf

```go
// Sprint formats using the default formats for its operands and returns the resulting string.
// Spaces are added between operands when neither is a string.
func Sprint(a ...interface{}) string {
    p := newPrinter()
    p.doPrint(a)
    s := string(p.buf)
    p.free()
    return s
}
```

## string转换成int，int32，int64

```go
// string转换成int64
strInt64, _ := strconv.ParseInt(str, 10, 64)
// string转换成int32
strInt32, _ := strconv.ParseInt(str, 10, 32)
// 这里strInt32实际上还是int64类型的，只是截取了32位，所以最终还是要强转一下变成int32类型，如果不强转成int32是会编译报错的
var realInt32 int32 = 0
realInt32 := int32(strInt32)
// string转换成int
strInt, err := strconv.Atoi(str)
```

