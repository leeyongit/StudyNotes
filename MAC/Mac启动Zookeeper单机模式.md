# Mac启动zookeeper单机模式

### 1. 安装zookeeper
```sh
> brew install zookeeper
> zkServer
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Usage: ./zkServer.sh {start|start-foreground|stop|restart|status|upgrade|print-cmd}
```

ZK 单机模式下，zoo.cfg 文件配置样本：
```sh
tickTime=2000
dataDir=/var/lib/zookeeper
clientPort=2181
```
### 2. 连接zookeeper
```sh
> zkCli
```
JLine 下，常用命令：

    # 获取帮助
    help
    # 查看子节点
    ls /
    # 查看节点信息
    get /
    # 创建节点，填充信息
    create /zk_test data
    # 更新节点数据， 修改信息
    set /zk_test new_date
    # 删除节点
    delete /zk_test

### 3. 查看zookeeper状态
```sh
> zkServer status
ZooKeeper JMX enabled by default
Using config: /usr/local/etc/zookeeper/zoo.cfg
Mode: standalone
```
这里的standalone指的是单机模式

### 3. 关闭zookeeper
执行$ `zkServer stop`:
