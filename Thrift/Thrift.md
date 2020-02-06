## Thrift

[Thrift](https://en.wikipedia.org/wiki/Apache_Thrift)是一个跨语言的服务部署框架，最初由Facebook于2007年开发，2008年进入Apache开源项目。Thrift通过一个中间语言(IDL, 接口定义语言)来定义RPC的接口和数据类型，然后通过一个编译器生成不同语言的代码（目前支持C++,Java, Python, PHP, Ruby, Erlang, Perl, Haskell, C#, Cocoa, Smalltalk和OCaml）,并由生成的代码负责RPC协议层和传输层的实现。

Thrift实际上是实现了C/S模式，通过代码生成工具将接口定义文件生成服务器端和客户端代码（可以为不同语言），从而实现服务端和客户端跨语言的支持。用户在Thirft描述文件中声明自己的服务，这些服务经过编译后会生成相应语言的代码文件，然后用户实现服务（客户端调用服务，服务器端提服务）便可以了。其中protocol（协议层, 定义数据传输格式，可以为二进制或者XML等）和transport（传输层，定义数据传输方式，可以为TCP/IP传输，内存共享或者文件共享等）被用作运行时库。

Thrift的协议栈如下图所示：

![img](https://images2018.cnblogs.com/blog/645085/201803/645085-20180304155305568-1935823842.png)

在Client和Server的最顶层都是用户自定义的处理逻辑，也就是说用户只需要编写用户逻辑，就可以完成整套的RPC调用流程。用户逻辑的下一层是Thrift自动生成的代码，这些代码主要用于结构化数据的解析,发送和接收，同时服务器端的自动生成代码中还包含了RPC请求的转发（Client的A调用转发到Server A函数进行处理）。

协议栈的其他模块都是Thrift的运行时模块：

- 底层IO模块，负责实际的数据传输，包括Socket，文件，或者压缩数据流等。
- TTransport负责以字节流方式发送和接收Message，是底层IO模块在Thrift框架中的实现，每一个底层IO模块都会有一个对应TTransport来负责Thrift的字节流(Byte Stream)数据在该IO模块上的传输。例如TSocket对应Socket传输，TFileTransport对应文件传输。
- TProtocol主要负责结构化数据组装成Message，或者从Message结构中读出结构化数据。TProtocol将一个有类型的数据转化为字节流以交给TTransport进行传输，或者从TTransport中读取一定长度的字节数据转化为特定类型的数据。如int32会被TBinaryProtocol Encode为一个四字节的字节数据，或者TBinaryProtocol从TTransport中取出四个字节的数据Decode为int32。
- TServer负责接收Client的请求，并将请求转发到Processor进行处理。TServer主要任务就是高效的接受Client的请求，特别是在高并发请求的情况下快速完成请求。
- Processor(或者TProcessor)负责对Client的请求做出相应，包括RPC请求转发，调用参数解析和用户逻辑调用，返回值写回等处理步骤。Processor是服务器端从Thrift框架转入用户逻辑的关键流程。Processor同时也负责向Message结构中写入数据或者读出数据。

Thrift的模块设计非常好，在每一个层次都可以根据自己的需要选择合适的实现方式。同时也应该注意到Thrift目前的特性并不是在所有的程序语言中都支持。例如C++实现中有TDenseProtocol没有TTupleProtocol，而Java实现中有TTupleProtocol没有TDenseProtocol。

利用Thrift用户只需要做三件事：

```
(1). 利用IDL定义数据结构及服务
(2). 利用代码生成工具将(1)中的IDL编译成对应语言（如C++、JAVA），编译后得到基本的框架代码
(3). 在(2)中框架代码基础上完成完整代码（纯C++代码、JAVA代码等）
```

为了实现上述RPC协议栈，Thrift定义了一套IDL，封装了server相关类, processor相关类,transport相关类,protocol相关类以及并发和时钟管理方面的库。下文将一一介绍。

### 数据类型

Thrift类型系统的目标是使编程者能使用完全在Thrift中定义的类型，而不论他们使用的是哪种编程语言。Thrift类型系统没有引入任何特殊的动态类型或包装器对象，也不要求开发者编写任何对象序列化或传输的代码。Thrift IDL文件在逻辑上，是开发者对他们的数据结构进行注解的一种方法，该方法告诉代码生成器怎样在语言之间安全传输对象，所需的额外信息量最小。

- Base Types（基本类型）

```
bool 布尔值，真或假
byte 有符号字节
i16  16位有符号整数
i32  32位有符号整数
i64  64位有符号整数
double 64位浮点数
string 与编码无关的文本或二进制字符串
```

许多语言中都没有无符号整数类型，且无法防止某些语言（如Python）的开发者把一个负值赋给一个整型变量，这会导致程序无法预料的行为。从设计角度讲，无符号整型鲜少用于数学目的，实际中更长用作关键词或标识符。这种情况下，符号是无关紧要的，可用有符号整型代替。

- Structs（结构体）

Thrift结构体定义了一个用在多种语言之间的通用对象。定义一个Thrift结构体的基本语法与C结构体定义非常相似。域可由一个整型域标识符（在该结构体的作用域内是唯一的），以及可选的默认值来标注。

```
struct Phone {
 1: i32 id,
 2: string number,
 3: PhoneType type
}
```

- enum(枚举）

```
enum Operation {
   ADD = 1,
   SUBTRACT = 2,
   MULTIPLY = 3,
   DIVIDE = 4
 }
```

- Containers（容器）

Thrift容器是强类型的，映射为通用编程语言中最常使用的容器。使用C++模板类来标注。有三种可用类型：

```
list<type>:映射为STL vector，Java ArrayList，或脚本语言中的native array。。
set<type>: 映射为为STL set，Java HashSet，Python中的set，或PHP/Ruby中的native dictionary。
Map<type1,type2>：映射为STL map，Java HashMap，PHP associative array，或Python/Ruby dictionary。
```

在目标语言中，定义将产生有read和write两种方法的类型，使用Thrift TProtocol对象对对象进行序列化和传输。

- Exceptions（异常）

异常在语法和功能上都与结构体相同，唯一的区别是它们使用exception关键词，而非struct关键词进行声明。 生成的对象继承自各目标编程语言中适当的异常基类，以便与任何给定语言中的本地异常处理无缝地整合。

```
exception InvalidOperation {
  1: i32 whatOp,
  2: string why
}
```

- Services（服务）

使用Thrift类型定义服务。对一个服务的定义在语法上等同于在面向对象编程中定义一个接口（或一个纯虚抽象类）。Thrift编译器生成实现该接口的客户与服务器存根。服务的定义如下：

```
service <name> {
<returntype> <name>(<arguments>)
[throws (<exceptions>)]
...
}
```

一个例子：

```protobuf
service StringCache {
	void set(1:i32 key, 2:string value),
	string get(1:i32 key) throws (1:KeyNotFound knf),
	void delete(1:i32 key)
}
```

注意： 除其他所有定义的Thrift类型外，void也是一个有效的函数返回类型。void函数可添加一个async修饰符，产生的代码不等待服务器的应答。 一个纯void函数会向客户端返回一个应答，保证服务器一侧操作已完成。应用开发者应小心，仅当方法调用失败是可以接受的，或传输层已知可靠的情况下，才使用async优化。



### 要编写Thrift定义文件，肯定要熟悉Thrift常见的数据类型：

1. 基本类型（括号内为对应的Java类型）：
   bool（boolean）: 布尔类型(TRUE or FALSE)
   byte（byte）: 8位带符号整数
   i16（short）: 16位带符号整数
   i32（int）: 32位带符号整数
   i64（long）: 64位带符号整数
   double（double）: 64位浮点数
   string（String）: 采用UTF-8编码的字符串

2. 特殊类型（括号内为对应的Java类型）：
   binary（ByteBuffer）：未经过编码的字节流

3. Structs（结构）：
   struct定义了一个很普通的OOP对象，但是没有继承特性。
   ```protobuf
   struct UserProfile {
   	1: i32 uid,
   	2: string name,
   	3: string blurb
   }
   ```
如果变量有默认值，可以直接写在定义文件里:
   ```protobuf
   struct UserProfile {
   	1: i32 uid = 1,
   	2: string name = "User1",
   	3: string blurb
   }
   ```

4. 容器，除了上面提到的基本数据类型，Thrift还支持以下容器类型：
   list(java.util.ArrayList)：
   set(java.util.HashSet)：
   map（java.util.HashMap）：

用法如下：
```protobuf
struct Node {
   1: i32 id,
   2: string name,
   3: list subNodeList,
   4: map subNodeMap,
   5: set subNodeSet
}
```

包含定义的其他Object:
```protobuf
struct SubNode {
   1: i32 uid,
   2: string name,
   3: i32 pid
}
```

```protobuf
struct Node {
   1: i32 uid,
   2: string name,
   3: list subNodes
}
```

5. Services服务，也就是对外展现的接口：
```protobuf
service UserStorage {
   void store(1: UserProfile user),
   UserProfile retrieve(1: i32 uid)
}
```