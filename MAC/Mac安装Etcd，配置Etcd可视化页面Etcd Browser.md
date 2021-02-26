# Mac安装Etcd，配置Etcd可视化页面Etcd Browser

## 一、Etcd
**类比于Zookeeper，开源的、分布式的键值对数据存储系统，主要用来做配置共享、服务的注册和发现。高可用，强一致性。内部采用 Raft 协议作为一致性算法选举leader，保证集群数据不丢失**
每个 etcd cluster 都是有若干个 member 组成的，每个 member 是一个独立运行的 etcd 实例，单台机器上可以运行多个 member。在正常运行的状态下，集群中会有一个 leader，其余的 member 都是 followers。leader 向 followers 同步日志，保证数据在各个 member 都有副本。leader 还会定时向所有的 member 发送心跳报文，如果在规定的时间里 follower 没有收到心跳，就会重新进行选举。客户端所有的请求都会先发送给 leader，leader 向所有的 followers 同步日志，等收到超过半数的确认后就把该日志存储到磁盘，并返回响应客户端。每个 etcd 服务有三大主要部分组成：raft 实现、WAL 日志存储、数据的存储和索引。WAL 会在本地磁盘（就是之前提到的 --data-dir）上存储日志内容（wal file）和快照（snapshot）

- 官方文档：https://coreos.com/etcd/
- etcd-browser(etcd-webui)： https://github.com/henszey/etcd-browser
- etckeeper：https://github.com/evildecay/etcdkeeper

## 二、Mac安装
用brew安装非常方便，没安装的自行安装Homebrew
```sh
brew search etcd
```
可以看到候选安装包
```sh
brew install etcd
```
执行etcd即可启动服务
```sh
etcd
```
可以看到以下信息：
- etcdserver: name = default name表示节点名称，默认为default
- data-dir保存日志和快照的目录，默认为当前工作目录default.etcd/
- 在http://localhost:2380和集群中其他节点通信
- 在http://localhost:2379提供HTTP API服务，供客户端交互。等会配置webui就是这个地址
- etcdserver: heartbeat = 100ms leader发送心跳到followers的间隔时间
- etcdserver: election = 1000ms 重新投票的超时时间，如果follow在该时间间隔没有收到心跳包，会触发重新投票，默认为1000ms

## 三、安装etcd webui
记得启动Etcd服务。
先安装node，git环境，然后clone
```sh
git clone https://github.com/henszey/etcd-browser.git
cd etcd-browser/
vim server.js
```
编辑server.js，修改内容如下：
```sh
var etcdHost = process.env.ETCD_HOST || '127.0.0.1';  # etcd 主机IP
var etcdPort = process.env.ETCD_PORT || 4001;          # etcd 主机端口
var serverPort = process.env.SERVER_PORT || 8000;      # etcd-browser 监听端口
```
然后启动
```sh
node server.js
```
访问：http://127.0.0.1:8000/

## 四、etcd命令行操作key
除了 HTTP API 外，etcd 还提供了 etcdctl 命令行工具和 etcd 服务交互。
`etcdctl get / --prefix --keys-only` 查看所有key
`etcdctl --endpoints=[172.31.22.xxx:2379] get / --prefix --keys-only` 查看所有key(参数加上etcd服务地址)
`etcdctl get /mechat/websocket/service/mapping` 查看某个key的值
`etcdctl --endpoints=[172.31.22.142:2379] get /mechat/websocket/service/mapping` 查看某个key的值

`etcdctl del /mechat/websocket/service/mapping` 删除某个key
`etcdctl put /mechat/websocket/service/mapping 'io.test.baidu.com:443->172.31.19.xxx:8085'` 修改某个key的值

