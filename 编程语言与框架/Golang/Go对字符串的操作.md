# Go对字符串的操作

## strings包方法
```go
// 转换
func ToUpper(s string) string
func ToLower(s string) string
func ToTitle(s string) string
func ToUpperSpecial(_case unicode.SpecialCase, s string) string
func ToLowerSpecial(_case unicode.SpecialCase, s string) string
func ToTitleSpecial(_case unicode.SpecialCase, s string) string
func Title(s string) string
------------------------------
// 比较
func Compare(a, b string) int
func EqualFold(s, t string) bool
------------------------------
// 清理
func Trim(s string, cutset string) string
func TrimLeft(s string, cutset string) string
func TrimRight(s string, cutset string) string
func TrimFunc(s string, f func(rune) bool) string
func TrimLeftFunc(s string, f func(rune) bool) string
func TrimRightFunc(s string, f func(rune) bool) string
func TrimSpace(s string) string
func TrimPrefix(s, prefix string) string
func TrimSuffix(s, suffix string) string
------------------------------
// 拆合
func Split(s, sep string) []string
func SplitN(s, sep string, n int) []string
func SplitAfter(s, sep string) []string
func SplitAfterN(s, sep string, n int) []string
func Fields(s string) []string
func FieldsFunc(s string, f func(rune) bool) []string
func Join(a []string, sep string) string
func Repeat(s string, count int) string
------------------------------
// 子串
func HasPrefix(s, prefix string) bool
func HasSuffix(s, suffix string) bool
func Contains(s, substr string) bool
func ContainsRune(s string, r rune) bool
func ContainsAny(s, chars string) bool
func Index(s, sep string) int
func IndexByte(s string, c byte) int
func IndexRune(s string, r rune) int
func IndexAny(s, chars string) int
func IndexFunc(s string, f func(rune) bool) int
func LastIndex(s, sep string) int
func LastIndexByte(s string, c byte) int
func LastIndexAny(s, chars string) int
func LastIndexFunc(s string, f func(rune) bool) int
func Count(s, sep string) int
------------------------------
// 替换
func Replace(s, old, new string, n int) string
func Map(mapping func(rune) rune, s string) string
------------------------------------------------------------
type Reader struct { ... }
func NewReader(s string) *Reader
func (r *Reader) Read(b []byte) (n int, err error)
func (r *Reader) ReadAt(b []byte, off int64) (n int, err error)
func (r *Reader) WriteTo(w io.Writer) (n int64, err error)
func (r *Reader) Seek(offset int64, whence int) (int64, error)
func (r *Reader) ReadByte() (byte, error)
func (r *Reader) UnreadByte() error
func (r *Reader) ReadRune() (ch rune, size int, err error)
func (r *Reader) UnreadRune() error
func (r *Reader) Len() int
func (r *Reader) Size() int64
func (r *Reader) Reset(s string)
------------------------------------------------------------
type Replacer struct { ... }
// 创建一个替换规则，参数为“查找内容”和“替换内容”的交替形式。
// 替换操作会依次将第 1 个字符串替换为第 2 个字符串，将第 3 个字符串
// 替换为第 4 个字符串，以此类推。
// 替换规则可以同时被多个例程使用。
func NewReplacer(oldnew ...string) *Replacer
// 使用替换规则对 s 进行替换并返回结果。
func (r *Replacer) Replace(s string) string
// 使用替换规则对 s 进行替换并将结果写入 w。
// 返回写入的字节数和遇到的错误。
func (r *Replacer) WriteString(w io.Writer, s string) (n int, err error)
```
上述的方式一和方式三都能实现此需求，但是方式三因为在strings包中没找到按照字符串索引位置截取的方法，所以在网上找到了一个自己实现的SubString截取字符串的方法。下面是具体的方法实现，更多字符串用法可以查看参考文章中的内容。

## 字符串的截取

```go
//截取字符串 start 起点下标 length 需要截取的长度
func Substr(str string, start int, length int) string {
    rs := []rune(str)
    rl := len(rs)
    end := 0
    if start < 0 {
        start = rl - 1 + start
    }
    end = start + length
    if start > end {
        start, end = end, start
    }
    if start < 0 {
        start = 0
    }
    if start > rl {
        start = rl
    }
    if end < 0 {
        end = 0
    }
    if end > rl {
        end = rl
    }
    return string(rs[start:end])
}
//截取字符串 start 起点下标 end 终点下标(不包括)
func Substr2(str string, start int, end int) string {
    rs := []rune(str)
    length := len(rs)
    if start < 0 || start > length {
        panic("start is wrong")
    }
    if end < 0 || end > length {
        panic("end is wrong")
    }
    return string(rs[start:end])
}
```

