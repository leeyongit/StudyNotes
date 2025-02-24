# 一文搞懂JDK8与Java1.8的区别

## 什么是Java？

 Java是一种广泛使用的计算机编程语言，拥有跨平台、面向对象、泛型编程的特性，广泛应用于企业级Web应用开发和移动应用开发。

 Java编程语言的风格十分接近C++语言。继承了C++语言面向对象技术的核心，舍弃了容易引起错误的指针，以引用取代；移除了C++中的运算符重载和多重继承特性，用接口取代；增加垃圾回收器功能。在Java SE 1.5版本中引入了泛型编程、类型安全的枚举、不定长参数和自动装/拆箱特性。

## 什么是JDK？

 Java Development Kit（JDK）是太阳微系统针对Java开发人员发布的免费软件开发工具包（SDK，Software development kit）。自从Java推出以来，JDK已经成为使用最广泛的Java SDK。

 作为Java语言的SDK，普通用户并不需要安装JDK来运行Java程序，而只需要安装JRE（Java Runtime Environment）。而程序开发者必须安装JDK来编译、调试程序。

![在这里插入图片描述](https://raw.githubusercontent.com/leeyongit/picGo/master/images/java_jdk.png)


 从上方的JDK各个版本发布时间和版本名称表也可以看到，Java大体有3大类命名方式：JDK、J2SE、JAVA SE。

#### (1)Java与JDK的区别

 JDK是个Java开发的工具包，Java是门编程语言。

#### (2)JDK8与JDK1.8的区别

 JDK8或者JDK1.8是由于自从JDK1.5/JDK5命名方式改变后遗留的新旧命令方式问题。所以JDK8或者JDK1.8也是同一个东西。

#### (3)JDK与J2SE的区别

 JAVA就是指JDK开发工具，所以我们可以理解为JAVA等价于JDK。JAVA有3个版本：J2SE J2EE J2ME，而J2SE是标准版本，J2ME是手机方向的，J2EE是网站开发方向的。

总结：
 简而言之：我们口中说的Java8、JDK8、JDK1.8都是同一个东西。