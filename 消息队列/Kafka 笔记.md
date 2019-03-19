Kafka 笔记
=========

## Kafka 配置项
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

### Topic-Level
- topic.flush.intervals.ms：覆盖log.default.flush.interval.ms。
- topic.log.retention.hours：覆盖log.retention.hours
- topic.partition.count.map：覆盖创建topic时指定的partition数量。
- topic.log.segment.bytes：覆盖log.segment.bytes
- topic.log.roll.{ms,hours}：覆盖log.roll.{ms,hours}
- topic.log.cleanup.policy：覆盖log.cleanup.policy
- min.cleanable.dirty.ratio：覆盖log.cleaner.min.cleanable.ratio
- min.insync.replicas：覆盖min.insync.replicas
- segment.jitter.ms：覆盖log.roll.jitter.{ms,hours}

## kafka-manager 出现错误

> Yikes! Ask timed out on [ActorSelection[Anchor(akka://kafka-manager-system/), Path(/user/kafka-manager)]] after [5000 ms]

解决方法: docker启动后，登陆到docker容器里
```sh
vim conf/application.conf

kafka-manager.zkhosts="zoo1:2181,zoo2:2182,zoo3:2183"
#kafka-manager.zkhosts=${?ZK_HOSTS}
```
将配置文件注掉一条，然后将zk的地址写入


## kafka修改日志保留时间参数（不用重启kafka）
默认7天 168小时 现在改成5天 
```sh
kafka-configs.sh –zookeeper localhost:2181 –entity-type topics –entity-name log –alter –add-config retention.ms=432000000
```
