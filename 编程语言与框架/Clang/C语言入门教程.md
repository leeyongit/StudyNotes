## C 语言入门教程

1. **安装 C 编译器**
在开始学习 C 语言之前,你需要安装一个 C 编译器。常用的编译器有 GCC (GNU Compiler Collection)、Microsoft Visual C++ 等。

2. **编写你的第一个 C 程序**
用文本编辑器创建一个新文件,例如 `hello.c`，并输入以下代码:
```c
#include <stdio.h>

int main() {
    printf("Hello, World!");
    return 0;
}
```
这个程序将在屏幕上打印 "Hello, World!"。

3. **编译和运行程序**
使用编译器编译程序:
```
gcc hello.c -o hello
```
这将生成一个名为 `hello` 的可执行文件。然后运行该程序:
```
./hello
```
你应该会看到输出 "Hello, World!"。

4. **基本语法和结构**
C 程序由函数和语句组成。`main()` 函数是 C 程序的入口点。`printf()` 函数用于输出文本到控制台。
每个语句都以分号 `;` 结束。大括号 `{}` 用于定义代码块。

5. **数据类型**
C 语言有以下基本数据类型:
- `int`: 整数
- `float`: 浮点数
- `double`: 双精度浮点数
- `char`: 字符
- `bool`: 布尔值(C99 及更高版本)

声明变量的语法:
```c
data_type variable_name;
```

6. **输入和输出**
使用 `scanf()` 函数读取用户输入:
```c
int num;
printf("Enter a number: ");
scanf("%d", &num);
```
使用 `printf()` 函数输出数据:
```c
printf("The number is: %d", num);
```

7. **控制流语句**
C 语言提供了以下控制流语句:
- `if-else` 语句
- `switch` 语句
- `for` 循环
- `while` 循环
- `do-while` 循环

8. **函数**
函数的定义语法:
```c
return_type function_name(parameter_list) {
    // function body
    return value;
}
```
调用函数:
```c
function_name(arguments);
```

9. **数组**
声明数组的语法:
```c
data_type array_name[size];
```
访问数组元素:
```c
array_name[index]
```

10. **指针**
指针是存储内存地址的变量。声明指针的语法:
```c
data_type *pointer_name;
```
使用指针访问值:
```c
*pointer_name
```

这个入门教程涵盖了 C 语言的基础知识。学习 C 语言还需要深入理解内存管理、结构体、文件 I/O 等更高级的概念。

