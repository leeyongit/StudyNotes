Nginx 限流配置
=============================
## 限流算法
### 令牌桶算法
![令牌桶算法](https://images2018.cnblogs.com/blog/802666/201805/802666-20180502140242681-53841293.jpg)

算法思想是：

- 令牌以固定速率产生，并缓存到令牌桶中；
- 令牌桶放满时，多余的令牌被丢弃；
- 请求要消耗等比例的令牌才能被处理；
- 令牌不够时，请求被缓存。

### 漏桶算法
![漏桶算法](https://images2018.cnblogs.com/blog/802666/201805/802666-20180502140248838-284301317.png)

算法思想是：

- 水（请求）从上方倒入水桶，从水桶下方流出（被处理）；
- 来不及流出的水存在水桶中（缓冲），以固定速率流出；
- 水桶满后水溢出（丢弃）。
- 这个算法的核心是：缓存请求、匀速处理、多余的请求直接丢弃。
相比漏桶算法，令牌桶算法不同之处在于它不但有一只“桶”，还有个队列，这个桶是用来存放令牌的，队列才是用来存放请求的。

从作用上来说，漏桶和令牌桶算法最明显的区别就是是否允许突发流量(burst)的处理，漏桶算法能够强行限制数据的实时传输（处理）速率，对突发流量不做额外处理；而令牌桶算法能够在限制数据的平均传输速率的同时允许某种程度的突发传输。


**Nginx按请求速率限速模块使用的是漏桶算法，即能够强行保证请求的实时处理速度不会超过设置的阈值。**

Nginx官方版本限制IP的连接和并发分别有两个模块：

- limit_req_zone 用来限制单位时间内的请求数，即速率限制,采用的漏桶算法 "leaky bucket"。
- limit_req_conn 用来限制同一时间连接数，即并发限制。

#### limit_req_zone 参数配置
```yaml
Syntax: limit_req zone=name [burst=number] [nodelay];
Default:    —
Context:    http, server, location
```
```yaml
limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
```
第一个参数：$binary_remote_addr 表示通过remote_addr这个标识来做限制，“binary_”的目的是缩写内存占用量，是限制同一客户端ip地址。
第二个参数：zone=one:10m表示生成一个大小为10M，名字为one的内存区域，用来存储访问的频次信息。
第三个参数：rate=1r/s表示允许相同标识的客户端的访问频次，这里限制的是每秒1次，还可以有比如30r/m的。
```yaml
limit_req zone=one burst=5 nodelay;
```
第一个参数：zone=one 设置使用哪个配置区域来做限制，与上面limit_req_zone 里的name对应。
第二个参数：burst=5，重点说明一下这个配置，burst爆发的意思，这个配置的意思是设置一个大小为5的缓冲区当有大量请求（爆发）过来时，超过了访问频次限制的请求可以先放到这个缓冲区内。
第三个参数：nodelay，如果设置，超过访问频次而且缓冲区也满了的时候就会直接返回503，如果没有设置，则所有请求会等待排队。

例子：
```yaml
http {
    limit_req_zone $binary_remote_addr zone=one:10m rate=1r/s;
    server {
        location /search/ {
            limit_req zone=one burst=5 nodelay;
        }
}
```
下面配置可以限制特定UA（比如搜索引擎）的访问：
```yaml
limit_req_zone  $anti_spider  zone=one:10m   rate=10r/s;
limit_req zone=one burst=100 nodelay;
if ($http_user_agent ~* "googlebot|bingbot|Feedfetcher-Google") {
    set $anti_spider $http_user_agent;
}
```
其他参数
```yaml
Syntax: limit_req_log_level info | notice | warn | error;
Default:
limit_req_log_level error;
Context:    http, server, location
```
当服务器由于limit被限速或缓存时，配置写入日志。延迟的记录比拒绝的记录低一个级别。例子：limit_req_log_level notice延迟的的基本是info。
```yaml
Syntax: limit_req_status code;
Default:
limit_req_status 503;
Context:    http, server, location
```
设置拒绝请求的返回值。值只能设置 400 到 599 之间。

#### ngx_htt  p_limit_conn_module 参数配置
这个模块用来限制单个IP的请求数。并非所有的连接都被计数。只有在服务器处理了请求并且已经读取了整个请求头时，连接才被计数。
```yaml
Syntax: limit_conn zone number;
Default:    —
Context:    http, server, location
limit_conn_zone $binary_remote_addr zone=addr:10m;
```
```yaml
server {
    location /download/ {
        limit_conn addr 1;
    }
```
一次只允许每个IP地址一个连接。
```yaml
limit_conn_zone $binary_remote_addr zone=perip:10m;
limit_conn_zone $server_name zone=perserver:10m;
```
```yaml
server {
    ...
    limit_conn perip 10;
    limit_conn perserver 100;
```
可以配置多个limit_conn指令。例如，以上配置将限制每个客户端IP连接到服务器的数量，同时限制连接到虚拟服务器的总数。
```yaml
Syntax: limit_conn_zone key zone=name:size;
Default:    —
Context:    http
```
```yaml
limit_conn_zone $binary_remote_addr zone=addr:10m;
```
在这里，客户端IP地址作为关键。请注意，不是$ remote_addr，而是使用$ binary_remote_addr变量。 $ remote_addr变量的大小可以从7到15个字节不等。存储的状态在32位平台上占用32或64字节的内存，在64位平台上总是占用64字节。对于IPv4地址，$ binary_remote_addr变量的大小始终为4个字节，对于IPv6地址则为16个字节。存储状态在32位平台上始终占用32或64个字节，在64位平台上占用64个字节。一个兆字节的区域可以保持大约32000个32字节的状态或大约16000个64字节的状态。如果区域存储耗尽，服务器会将错误返回给所有其他请求。
```yaml
Syntax: limit_conn_log_level info | notice | warn | error;
Default:
limit_conn_log_level error;
Context:    http, server, location
```
当服务器限制连接数时，设置所需的日志记录级别。
```yaml
Syntax: limit_conn_status code;
Default:
limit_conn_status 503;
Context:    http, server, location
```
设置拒绝请求的返回值。

[实战](https://www.cnblogs.com/biglittleant/p/8979915.html)

## 关于limit_req和limit_conn的区别

**1. 首先，limit_req和limit_conn两个模块都是为了来限流的，但是两者不在一个层面，为了搞清楚这个，必须先要弄清楚request和connection的区别，因为在很多情况下，我们把他们混淆了。**
so, what is the difference  between connection and request? 

**2. connection是连接，即常说的tcp连接，通过三次握手而建立的一个完整状态机。建立一个连接，必须得要三次握手。request是指请求，即http请求，（注意，tcp连接是有状态的，而构建在tcp之上的http却是无状态的协议）。**

通过打开一个网页，然后通过wareshark可以看到，一个连接建立后（即三次握手后），在这个连接断开之前（即四次挥手之前），会有很多的http request，这就是他们的区别：
即一个连接的生命周期中，会存在一个或者多个请求，这是为了加快效率，避免每次请求都要三次握手建立连接，现在的HTTP/1.1协议都支持这种特性，叫做keepalive。

区别：一个连接的生命周期中，会存在一个或者多个请求

**3. 那么在Nginx中，对于连接限制模块：limit_conn_module来看：**

```
limit_conn_zone $binanry_remote_addr zone=conn_zone:1m;
locoation /limit.html {
    limit_conn conn_zone 1;
}
```
这样的配配置，表明以ip为key，来限制每个ip访问lmit.html文件时候，最多只能有一个在线，否则其余的都要返回不可用。

这种情况就是一个静止状态的计数可以实现，而无关乎多长时间。

举个例子，如果你的这个连接一直不释放，即使你通过这一个连接发送出再多的request请求，只要我能够应付，那么我就帮你处理。但是，如果你只需要处理2个请求，但是这两个请求是分别用两个连接同时发送过来的，那么，我就只能处理其中一个，另外一个就不行。这就是他的区别。

可以用ab命令来测试：
~ ab -n100 -c100 -k http://yoursit/limit.html
注意：ab命令的-n -c都是指的发送多少请求，即－n一共发送多少请求，-c同时发送多少请求，他并不关心需要多少连接来发送，默认情况下是每个请求都建立一个连接来发送。
上面这个命令，由于默认是一个连接发送一个请求，那么这将会同时建立100个连接，而这样的话，就会导致限制超过（最多只能一个连接for一个ip）。99个请求都会失败。

而如果你只开一个连接：
~ ab -n100 -c100 -k http://yousit/limt.html
这里的-k选秀就是表示keepalive，只开一个连接来发送这100个请求，即使是同时发送，那么server也不会认为你超过了，因为在一个时间你只是建立一个连接，这样这100个请求都会干净利落的处理完成。

**4. limit_req_module**
```sh
limit_req_zone $binary_remtoe_addr zone=req_zone:1m rate=1r/s;
location /limit.html{
    limit_conn zone=req_zone;
}
```
注意和上面的区别：这里需要为共享内存配置一个速率rate
表明：对于每个ip来说，处理请求的速度不超过每秒1个请求。

可以看到这是个速度量（而上面的那个是数字量，速度和个数还是有直观的区别的吧。。）
那么这时候 :
~ ab -n100 -c100 http://yoursit/limit.html 
和
~ ab -n100 -c100 -k http://yousit/limt.html
的区别就不是那么明显了，因为这总是同时发送出100个请求（不管是通过100个连接还是1个连接），只要你请求到底的速度超过每秒1个，那么我就会拒绝你。
