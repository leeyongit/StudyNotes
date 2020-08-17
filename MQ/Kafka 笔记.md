# Kafka 笔记

**Kafka 配置项**

这里只记录一些目前使用Kafka可能会比较有用的配置项。关于单个broker的，和关于topic-level的。其中有些配置项可以是topic级别的。
Kafka的版本是0.8.2.1。
- log.flush.interval：默认500，数据积累的量，超过这个量就将数据写到磁盘
- log.default.flush.interval.ms：数据写到磁盘的间隔时间，毫秒。默认与log.default.flush.scheduler.interval.ms相同，3000。
- log.retention.hours：数据保留的时间，小时。默认168
- log.segment.bytes：数据文件大小，默认是102410241024
- log.roll.{ms,hours}：数据文件切分的时间间隔，与大小限制同时起作用的，默认是24*7小时
- log.cleanup.policy：处理切分后数据的方式，delete或者compact。如果是delete，被切分出来的数据会被删除；如果是compact，切分出的数据会被压缩用于淘汰过期的数据。
- log.cleaner.min.cleanable.ratio：
- min.insync.replicas：
- log.roll.jitter.{ms,hours}：

**Topic-Level**
- topic.flush.intervals.ms：覆盖log.default.flush.interval.ms。
- topic.log.retention.hours：覆盖log.retention.hours
- topic.partition.count.map：覆盖创建topic时指定的partition数量。
- topic.log.segment.bytes：覆盖log.segment.bytes
- topic.log.roll.{ms,hours}：覆盖log.roll.{ms,hours}
- topic.log.cleanup.policy：覆盖log.cleanup.policy
- min.cleanable.dirty.ratio：覆盖log.cleaner.min.cleanable.ratio
- min.insync.replicas：覆盖min.insync.replicas
- segment.jitter.ms：覆盖log.roll.jitter.{ms,hours}

**kafka配置中request.required.acks含义**

> Kafka producer的ack有3中机制，初始化producer时的producerconfig可以通过配置request.required.acks不同的值来实现。

0：这意味着生产者producer不等待来自broker同步完成的确认继续发送下一条（批）消息。此选项提供最低的延迟但最弱的耐久性保证（当服务器发生故障时某些数据会丢失，如leader已死，但producer并不知情，发出去的信息broker就收不到）。

1：这意味着producer在leader已成功收到的数据并得到确认后发送下一条message。此选项提供了更好的耐久性为客户等待服务器确认请求成功（被写入死亡leader但尚未复制将失去了唯一的消息）。

-1：这意味着producer在follower副本确认接收到数据后才算一次发送完成。
此选项提供最好的耐久性，我们保证没有信息将丢失，只要至少一个同步副本保持存活。

三种机制，性能依次递减 (producer吞吐量降低)，数据健壮性则依次递增。




### kafka-manager 出现错误

> Yikes! Ask timed out on [ActorSelection[Anchor(akka://kafka-manager-system/), Path(/user/kafka-manager)]] after [5000 ms]

解决方法: docker启动后，登陆到docker容器里
```sh
vim conf/application.conf

kafka-manager.zkhosts="zoo1:2181,zoo2:2182,zoo3:2183"
#kafka-manager.zkhosts=${?ZK_HOSTS}
```
将配置文件注掉一条，然后将zk的地址写入



**kafka修改日志保留时间参数（不用重启kafka）**

默认7天 168小时 现在改成1天
```sh
kafka-configs.sh –zookeeper localhost:2181 –entity-type topics –entity-name log –alter –add-config retention.ms=86400
```


**ZooKeeper 在硬盘满后，无法再次启动，抛出Last transaction was partial.**

Bug见：https://issues.apache.org/jira/browse/ZOOKEEPER-1621
首先我的环境是单节点，ZooKeeper的版本是3.4.8。
因为是单节点，ZooKeeper无法启动影响非常大，多节点也有可能出现同时硬盘都写满的情况，如果问题在线上发生，后果不堪设想。
折腾了一下，发现，把ZooKeeper安装目录下的data/log/version-2下的，大小为0（异常的）日志，删除掉后，再重启 ，问题解决！


