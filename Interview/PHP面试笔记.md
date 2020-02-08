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

### 三、php的生命周期

PHP 生命周期关键词

1. MINIT（module init 模块初始化）

- 启动 PHP 输出、初始化垃圾回收器
- 启动 Zend 引擎、注册 Zend 核心扩展、Zend 标准常量
- 解析 PHP.ini，映射 PHP.ini 相关配置
- 注册静态、动态编译的扩展
- 回调各扩展定义的 module starup 钩子函数

用一句话来说，就是初始化类、常量、扩展等 PHP 所用到的资源

2. RINIT（request init 请求初始化）

PHP 初始化脚本执行的基本环境，SAPI 将控制权交给 PHP，激活 Zend 引擎，初始化执行器

3. PHP 脚本执行

Zend 引擎接管控制权，将 PHP 脚本编译成 Opcodes，并顺序执行

4. RSHUTDOWN (request shutdown 请求结束)

PHP 脚本执行完之后进入请求结束阶段，PHP 启动清理程序，这个阶段，将 flush 输出内容，发送 http 响应内容，关闭 PHP 执行器

5. MSHUTDOWN(module shutdown 模块关闭)

这个阶段主要是进行资源的清理、php 各模块的关闭操作，同时，将回调各扩展的 module shutdown 钩子函数

### 四、php的垃圾回收机制(zval)
[PHP垃圾回收机制](https://www.jianshu.com/p/e39ce8aafe52)

5. memcache 和redis的内存分布原理 (lru算法)

7. 手写单例

8. 手写快速排序

9. memcache的hash一致性原理

10. 分析nginx中的access.log 查找出 某个时间段内ip地址 请求次数最高的 `awk '{print $7} |sort|uniq -c |sort -n '`

11. 讲述php-fpm的运行原理

12. 讲述一个请求在执行过程中 进过了哪些流程(网络-dns-负载nginx-应该程序)

13. mysql索引原理

14. mysql事务的 4个一致性 和 隔离性(脏读 和可重读)

15. nginx调优

16. mysql的聚集索引和非聚集索引

17. nginx的几种负载方式

18. Nginx中的epoll 和select, poll模式的区别

19. php-fpm的2中通信模式  tcp socket 和 unix socket

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
