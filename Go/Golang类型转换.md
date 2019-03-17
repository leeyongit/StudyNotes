# go语言string、int、int64互相转换

```golang
// string到int  
int,err:=strconv.Atoi(string)  
// string到int64  
int64, err := strconv.ParseInt(string, 10, 64)  
// int到string  
string:=strconv.Itoa(int)  
// int64到string  
string:=strconv.FormatInt(int64,10)  
```