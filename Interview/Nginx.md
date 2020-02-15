# Nginx
### nginx调优

### nginx 负载均衡的几种方式?
- 循环机制 – 循环分发对应用服务器的请求
- 最少连接机制 – 将请求发送给连接数最少的服务器
- ip-hash机制 – 哈西函数用于确定请求被配那个服务器（基于客户端IP地址）

### Nginx中的epoll 和select, poll模式的区别
select，poll，epoll都是IO多路复用的机制。I/O多路复用就通过一种机制，可以监视多个描述符，一旦某个描述符就绪（一般是读就绪或者写就绪），能够通知程序进行相应的读写操作。但select，poll，epoll本质上都是同步I/O，因为他们都需要在读写事件就绪后自己负责进行读写，也就是说这个读写过程是阻塞的，而异步I/O则无需自己负责进行读写，异步I/O的实现会负责把数据从内核拷贝到用户空间。关于这三种IO多路复用的用法，前面三篇总结写的很清楚，并用服务器回射echo程序进行了测试。连接如下所示：

select：http://www.cnblogs.com/Anker/archive/2013/08/14/3258674.html
poll：http://www.cnblogs.com/Anker/archive/2013/08/15/3261006.html
epoll：http://www.cnblogs.com/Anker/archive/2013/08/17/3263780.html
