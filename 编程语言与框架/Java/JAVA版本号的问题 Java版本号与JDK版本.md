## JAVA版本号的问题 Java版本号与JDK版本

初学Java时便一直疑惑Java版本号到底是如何命名的？时常在网上看到Java5、Java6、Java7、Java8 (到今天已经到了Java12了，2019.4.5) 这一类 “Java X” 的Java版本名称，同时又会看到诸如JDK1.5、JDK1.6这中 “JDK1.X” 的JDK叫法。一直以来都在纠结Java以及JDK的规范版本名称到底是如何，直到最近在几本书上看到了相关的解释才有点明白，现总结在这里：

首先1996年发布了最初版本Java1.0（此前原型为1995年的Oak，因Oak商标已被占用后改名为Java），此后为Java1.1、J2SE1.2、J2SE1.3、J2SE1.4、采用 1.X的命名方式，直到2004年的JavaOne会议后版本数提升为5.0，这一新版本为Java SE5.0（或J2SE5.0，此处存在疑问，网上使用较多为Java SE5），在2006年Sun公司终结了已经有8年历史的J2SE、J2EE、J2ME的命名方式启用了今天的 Java SE、Java EE、Java ME  命名方式，而此后的版本为Java SE6、Java SE7、Java SE8、Java SE9、Java SE10、Java SE11、Java SE12。

而JDK则在 Java1.0 到 Java9 对应每一个版本号 ：JDK1.0、JDK1.2 ... JDK1.8、JDK1.9，Java10以后JDK对应名称为：JDk10、JDK11、JDK12。

时间线如下： 

Java SE版本时间线

| Java SE版本 | JDK版本 | 发布时间   | 开发代号            |
| ----------- | ------- | ---------- | ------------------- |
| Oak         |         | 1995-05-23 | Oak(橡树)           |
| Java 1.0    | JDK1.0  | 1996-01-23 |                     |
| Java 1.1    | JDK1.1  | 1997-02-18 |                     |
| J2SE 1.2    | JDK1.2  | 1998-12-04 | Playground（运动场  |
| J2SE 1.3    | JDK1.3  | 2000-05-08 | Kestrel（美洲红隼） |
| J2SE 1.4    | JDK1.4  | 2002-02-13 | Merlin（灰背隼）    |
| Java SE 5.0 | JDK1.5  | 2004-09-29 | Tiger（老虎）       |
| Java SE 6   | JDK1.6  | 2006-12-11 | Mustang（野马）     |
| Java SE 7   | JDK1.7  | 2011-07-28 | Dolphin（海豚）     |
| Java SE 8   | JDK1.8  | 2014-03-18 | Spider（蜘蛛）      |
| Java SE 9   | JDK1.9  | 2017-09-21 |                     |
| Java SE 10  | JDK10   | 2018-03-21 |                     |
| Java SE 11  | JDK11   | 2018-09-25 |                     |
| Java SE 12  | JDK12   | 2019-3-20  |                     |


​			 
下图来自《Java核心技术》卷1 第2章 2.1.1

![img](https://img-blog.csdnimg.cn/20190405160849110.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NDk5ODQw,size_16,color_FFFFFF,t_70)

> 你已经看到， JDK 是 Java Development Kit 的缩写。有点混乱的是： 这个工具包的版本1.2 ~ 版本 1.4 被称为 Java SDK (软件开发包， Software Development Kit )。 在某些场合下，
> 还可以看到这个过时的术语。另外， 还有一个术语是 Java 运行时环境（ JRE ), 它包含虚拟机
> 但不包含编译器。这并不是开发者想要的环境， 而是专门为不需要编译器的用户而提供。
> 接下来， Java SE 会大量出现， 相对于 Java EE ( Enterprise Edition) 和 Java ME ( Micro
> Edition ), 它是 Java 的标准版。
>     Java 2 这种提法始于 1998 年。当时 Sim 公司的销售人员感觉增加小数点后面的数值改
> 变版本号并没有反映出 JDK 1.2 的重大改进。但是，由于在发布之后才意识到这个问题， 所
> 以决定开发工具包的版本号仍然沿用 1.2, 接下来的版本是 1.3、 1.4 和 5.0, 但是， Java 平台
> 被重新命名为 Java 2。因此， 就有了 Java 2 Standard Edition Software Development Kit ( Java 2
> 标准版软件开发包）的 5.0 版，即 J2SE SDK 5.0。
>     幸运的是，2006 年版本号得到简化。 Java 标准版的下一个版本取名为 Java SE 6, 后来
> 又有了 Java SE 7 和 Java SE 8。不过，“- 内部” 版本号分别是 1.6.0、 1.7.0 和 1.8.0。
> 当 Oracle 为解决一些紧急问题做出某些微小的版本改变时， 将其称为更新。 例如： Java
> SE 8u31 是 JavaSE 8 的第 31 次更新， 它的内部版本号是 1.8.0_31。 更新不需要安装在前一
> 个版本上，它会包含整个 JDK 的最新版本。 另外， 并不是所有更新都公开发布， 所以如果
> “ 更新 31” 之后没有“ 更新 32”，你也不用惊慌。
>
> ----引自《Java核心技术》卷1 第2章 2.1.1

