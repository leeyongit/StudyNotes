
```
Yikes! Ask timed out on [ActorSelection[Anchor(akka://kafka-manager-system/), Path(/user/kafka-manager)]] after [5000 ms]
```
## 解决方法
docker启动后，登陆到docker容器里
```sg
vim conf/application.conf

kafka-manager.zkhosts="zoo1:2181,zoo2:2182,zoo3:2183"
#kafka-manager.zkhosts=${?ZK_HOSTS}
```
将配置文件注掉一条，然后将zk的地址写入