PHP面试题
=====

### 一、行列递增矩阵(杨氏矩阵)的查找、在m*n列数组

有一个二维数组. 数组的每行从左到右是递增的，每列从上到下是递增的. 在这样的数组中查找一个数字是否存在。杨氏矩阵说白了，杨氏矩阵就是一个二维数组，不过这个二维数组有点特别：数组的每行从左到右是递增的，每列从上到下是递增的。

#### 解法一：分治法

以查找数6为例，因为矩阵的行和列都是递增的，所以整个矩阵的对角线上的数也是递增的，故可以在对角线上进行二分查找：如果要找的数的大小介于对角线上相邻的两个数之间，则可以排除掉左上和右下的两个小矩阵，而在左下和右上的两个小矩阵内继续递归查找，如图4-2所示。

![img](https://img-blog.csdn.net/20160403103402050?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)

#### 解法二：定位法

如图4-3，首先直接定位到矩阵中最右上角的元素，如果这个元素比要找的数6大就往左走，比要找的数6小就往下走，直到找到要找的数6为止。这个方法的时间复杂度是O(m +n)。

![img](https://img-blog.csdn.net/20160403103431409?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQv/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/Center)


### 二、 2个很大的文件、查找相同的内容

比较两个文件： comm file1 file2
只显示file1独有的行：comm -2 -3 file1 file2
只显示file2独有的行：comm -1 -3 file1 file2
**只显示两者重复的行：comm -1 -2 file1 file2**
只显示两者不重复的行：comm -3 file1 file2 | sed 's/^\t//' 后面的sed是将以\t开头的\t去掉：



**有哪些数据结构？**
顺序存储、链表存储、索引存储、散列存储常用的数据结构：
数组
栈（先进后出、线性表）
队列（先进先出、后进后出、线性表）
链表（每个节点包括两个部分：一个存储数据元素的数据域、另一个存储下一个节点地址的指针域）
树
图
堆（是一种动态的树形结构）
哈希表（散列表）

7. 手写单例
单例模式：保证一个类仅有一个实例，并提供一个访问它的全局访问方法。
```php
<?php
class Mysql {
    // 该属性用来保存实例
    private static $conn;
    // 构造函数为private, 防止创建对象
    private function __construct() {
        $this->conn = mysqli_connect("localhost","root","password","dbname");
    }
    // 创建一个用来实例化的方法
    public static function getInstance() {
        if (!(self::$conn instanceof self)) {
            self::$conn = new self;
        }
        return self::$conn;
    }
    public function __clone() {
        trigger_error('Clone is not allowed!');
    }
}
// 只能这样取的实例，不能new和clone
$mysql = Mysql::getInstance();
```



10. 分析nginx中的access.log 查找出 某个时间段内ip地址 请求次数最高的
```sh
awk '{print $1}' access.log |sort|uniq -c|sort -k1nr|head -n 1
```
awk默认按空格进行切分，$1为ip位置
sort 参数：
    -n: numeric-sort，根据字符串数值进行排序
    -r: reverse，反向输出排序结果
    -k N: key，以第N列进行排序
uniq -c：可以统计重复行出现的次数

11. 讲述php-fpm的运行原理
php-fpm是一种master（主）/worker（子）多进程架构。master进程主要负责CGI及PHP环境初始化、事件监听、子进程状态等等，worker进程负责处理php请求。
从FPM接收到请求，到处理完毕，其具体的流程如下：
（1）FPM的master进程接收到请求。
（2）master进程根据配置指派特定的worker进程进行请求处理，如果没有可用进程，返回错误，这也是我们配合Nginx遇到502错误比较多的原因。
（3）worker进程处理请求，如果超时，返回504错误。
（4）请求处理结束，返回结果。




20. tcp的三次握手，能不能二次握手 为什么

21. 网络7层

22. 幂等，CAP原则，分布式一致性

- redis分布式
- 分布式系统原理、数据的一致性
- 集群
- 并发编程、高并发

### 谈谈你对网站性能优化的看法？
    第一，前端层面，必须提到浏览器缓存、尽可能减少请求数量、使用延迟加载（ajax技术）、使用cdn缓存、压缩静态文件等；
    第二，web服务器层面，必须提到开启gzip压缩技术、卸载web服务器不必要的模块、开启服务器缓存等；
    第三，PHP语言层面，开启编译缓存、使用PHP扩展实现计算密集型模块、使用HHVM或PHP7替代旧版本PHP、修改php.ini参数对PHP性能调优、减少高消耗函数使用等；
    第四，数据库MySQL层面，合理创建索引、合理设计数据表结构、尽可能减少数据库连接、对数据库内常用数据进行缓存、读写分离、垂直（水平）分库分表等；
    第五，非关系型数据库，使用非关系数据库减少数据库链接、保持数据库与非关系数据库数据一致性等
