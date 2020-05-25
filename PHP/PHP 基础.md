# PHP 基础

1. ==（等）和===（恒等）的区别
恒等计算符,和比较运算符号“==”的区别是 “==”不会检查条件式的表达式的类型,恒等计算符会同时检查表达式的值与类型。

2. 魔术函数理解
```php
**__sleep() 和 __wakeup()**
serialize() 函数会检查类中是否存在一个魔术方法 __sleep()。
unserialize() 会检查是否存在一个 __wakeup() 方法
**__toString()**
__toString() 方法用于一个类被当成字符串时应怎样回应。例如 echo $obj; 应该显示些什么。此方法必须返回一个字符串，否则将发出一条 E_RECOVERABLE_ERROR 级别的致命错误。
**__invoke()**
当尝试以调用函数的方式调用一个对象时，__invoke() 方法会被自动调用。
**__set_state()**
自 PHP 5.1.0 起当调用 var_export() 导出类时，此静态 方法会被调用。
```
3. static、$this、self 区别
```php
self :转发调用的一种。使用 self:: 或者 __CLASS__ 对当前类的静态引用，取决于定义当前方法所在的类
static :转发调用的一种。用于后期静态绑定，也可以称之为“静态绑定”，因为它可以用于（但不限于）静态方法的调用。
$this :指向当前类对象的实例，也就是对当前实例的一个引用。在静态方法中不能使用$this关键字，因为静态方法是属于类的，可供类直接调用，而其在调用时可能并没有创建对象
[php方法调用中self，this，static的区别](https://juejin.im/post/5af11926f265da0b9a69e80f)
```
4. OOP 思想
面向对象编程是一种计算机编程架构，其特征是封装性、继承性、多态性，达到了软件工程的三个主要目标：重用性、灵活性和扩展性。

5. 面向对象五大基本设计原则
```php
* 单一职责原则（SRP）  一个类只负责一个职责
* 开闭原则（OCP）     软体实体应当对扩展开放，对修改关闭
* 里氏代换原则（LSP）  子类可以替换父类，并且不会导致程序错误
* 接口隔离原则（ISP）  客户端不应该依赖它不需要的接口，一个类对另一个类的依赖应该建立在最小的接口上。
* 依赖倒置原则（DIP）  高层次的模块不应该依赖于低层次的模块，它们应该依赖于抽象。抽象不应该依赖于具体，具体应该依赖于抽象。
```

6. 抽象类和接口的区别，使用场景
```php
1、相同点
     A. 两者都是抽象类，都不能实例化。
     B. interface实现类及abstrct class的子类都必须要实现已经声明的抽象方法。

2.、不同点
     A. interface需要实现，要用implements，而abstract class需要继承，要用extends。
     B. 一个类可以实现多个interface，但一个类只能继承一个abstract class。
     C. interface强调特定功能的实现，而abstract class强调所属关系。 
     D. 尽管interface实现类及abstrct class的子类都必须要实现相应的抽象方法，但实现的形式不同。interface中的每一个方法都是抽象方法，都只是声明的(declaration, 没有方法体)，实现类必须要实现。而abstract class的子类可以有选择地实现。
```
7. Trait
Trait 是为类似 PHP 的单继承语言而准备的一种代码复用机制。Trait 为了减少单继承语言的限制，使开发人员能够自由地在不同层次结构内独立的类中复用 method。Trait 和 Class 组合的语义定义了一种减少复杂性的方式，避免传统多继承和 Mixin 类相关典型问题。
```php
trait ezcReflectionReturnInfo {
    function getReturnType() { /*1*/ }
    function getReturnDescription() { /*2*/ }
}

class ezcReflectionMethod extends ReflectionMethod {
    use ezcReflectionReturnInfo;
    /* ... */
}

class ezcReflectionFunction extends ReflectionFunction {
    use ezcReflectionReturnInfo;
    /* ... */
}
```

8. echo、print、print_r 区别(区分出表达式与语句的区别)
```php
可以理解为：
print 是打印字符串
print_r 则是打印复合类型 如数组 对象等
在PHP中的执行速率从快到慢为：echo(),   print(),   print_r()

echo是PHP语句, print和print_r是函数,语句没有返回值,函数可以有返回值(即便没有用)
```
9. 常见 HTTP 状态码，分别代表什么含义
```php
2xx成功
3xx重定向
    301 Moved Permanently 永久重定向
    302 Found 临时重定向
4xx客户端错误
    403 Forbidden 服务器已经理解请求，但是拒绝执行它。
    404 Not Found
    405 Method Not Allowed 请求行中指定的请求方法不能被用于请求相应的资源
5xx服务器错误
    502 Bad Gateway 由于临时的服务器维护或者过载，服务器当前无法处理请求。
    504 Gateway Timeout 作为网关或者代理工作的服务器尝试执行请求时，未能及时从上游服务器（URI标识出的服务器，例如HTTP、FTP、LDAP）或者辅助服务器（例如DNS）收到响应
```