Go基本数据类型总结
---------------
- 基本类型：boolean，numeric，string类型的命名实例是预先声明的。
- 复合类型：array，struct，指针，function，interface，slice，map，channel类型（可以使用type构造）。

## Numeric types

```
A numeric type represents sets of integer or floating-point values. The predeclared architecture-independent numeric types are:
uint8       the set of all unsigned  8-bit integers (0 to 255)
uint16      the set of all unsigned 16-bit integers (0 to 65535)
uint32      the set of all unsigned 32-bit integers (0 to 4294967295)
uint64      the set of all unsigned 64-bit integers (0 to 18446744073709551615)
int8        the set of all signed  8-bit integers (-128 to 127)
int16       the set of all signed 16-bit integers (-32768 to 32767)
int32       the set of all signed 32-bit integers (-2147483648 to 2147483647)
int64       the set of all signed 64-bit integers (-9223372036854775808 to 9223372036854775807)
float32     the set of all IEEE-754 32-bit floating-point numbers
float64     the set of all IEEE-754 64-bit floating-point numbers
complex64   the set of all complex numbers with float32 real and imaginary parts
complex128  the set of all complex numbers with float64 real and imaginary parts
byte        alias for uint8
rune        alias for int32
The value of an n-bit integer is n bits wide and represented using two's complement arithmetic.
There is also a set of predeclared numeric types with implementation-specific sizes:
uint            either 32 or 64 bits
int             same size as uint
uintptr         an unsigned integer large enough to store the uninterpreted bits of a pointer value
To avoid portability issues all numeric types are distinct except byte, which is an alias for uint8, and rune, which is an alias for int32. Conversions are required when different numeric types are mixed in an expression or assignment. 
For instance, int32 and int are not the same type even though they may have the same size on a particular architecture.
```

1. int类型中哪些支持负数
> 有符号（负号）：int8 int16 int32 int64
> 无符号（负号）：uint8 uint16 uint32 uint64
2. 浮点类型的值有float32和float64(没有 float 类型)
3. byte和rune特殊类型是别名
> byte就是unit8的别名
> rune就是int32的别名
4. int和uint取决于操作系统（32位机器上就是32字节，64位机器上就是64字节）
> uint是32字节或者64字节
> int和uint是一样的大小
5. 为了避免可移植性问题，除了byte（它是uint8的别名）和rune（它是int32的别名）之外，所有数字类型都是不同的。 在表达式或赋值中混合使用不同的数字类型时，需要转换。例如，int32和int不是相同的类型，即使它们可能在特定架构上具有相同的大小。

所以上面的文档解释了为什么int，int32，int64之间需要进行类型转换才能进行运算。

## String types
```
A string type represents the set of string values. A string value is a (possibly empty) sequence of bytes. Strings are immutable: once created, it is impossible to change the contents of a string. The predeclared string type is string.
The length of a string s (its size in bytes) can be discovered using the built-in function len. The length is a compile-time constant if the string is a constant. A string's bytes can be accessed by integer indices 0 through len(s)-1. It is illegal to take the address of such an element; if s[i] is the i'th byte of a string, &s[i] is invalid.
```
字符串是不可变的：一旦创建，就不可能改变字符串的内容。 预先声明的字符串类型是字符串。

可以使用内置函数len来发现字符串s的长度（以字节为单位的大小）。 如果字符串是常量，则length是编译时常量。 字符串的字节可以通过整数索引0到len（s）-1来访问。 取这种元素的地址是非法的; 如果s [i]是字符串的第i个字节，则＆s [i]无效。

## Map Types
```go
make(map[string]int)
make(map[string]int, 100)
The initial capacity does not bound its size: maps grow to accommodate the number of items stored in them, with the exception of nil maps. A nil map is equivalent to an empty map except that no elements may be added.
```
初始容量不限制其大小：map增长以适应存储在其中的项目数，除了nil map。 nil map等价于空map，不能添加元素。

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