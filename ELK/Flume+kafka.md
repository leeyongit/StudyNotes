# 日志/消息整体流向Flume => kafka => logstash => elasticsearch => kibana

## 安装JDK 
### 用yum安装JDK
1.查看yum库中都有哪些jdk版本(暂时只发现了openjdk)
```sh
java -version
yum search java|grep jdk
```
### 选择版本,进行安装
```sh
yum install java-1.8.0-openjdk
```
安装完之后，默认的安装目录是在: /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre

### 设置java的环境变量
vi /etc/profile
```sh
#set java environment
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/jre
          /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64/jre
JRE_HOME=$JAVA_HOME
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/bin:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```
### 让修改生效
source /etc/profile

## 安装Flume

从官网下载Flume 二进制安装包，解压安装：
```sh
wget http://mirror.bit.edu.cn/apache/flume/1.8.0/apache-flume-1.8.0-bin.tar.gz
tar zxvf apache-flume-1.8.0-bin.tar.gz
mv apache-flume-1.8.0-bin flume
cd flume
```

### 启动flume
```sh
nohup bin/flume-ng agent --conf-file  conf/kafka.properties -c conf/ --name agent -Dflume.root.logger=DEBUG,console 1>/dev/null 2>&1 &
```

## Kafka

[kafka教程](https://www.w3cschool.cn/apache_kafka/apache_kafka_introduction.html)

### 安装kafka
```sh
wget  https://mirrors.tuna.tsinghua.edu.cn/apache/kafka/2.1.0/kafka_2.12-2.1.0.tgz
tar -zxf kafka_2.12-2.1.0.tgz
mv kafka_2.12-2.1.0 /usr/local/
cd /usr/local/kafka_2.12-2.1.0/
cp config/server.properties config/server-1.properties
cp config/server.properties config/server-2.properties
```

### 编辑这些新文件和设置以下属性：
```sh
config/server-1.properties:
    broker.id=1
    listeners=PLAINTEXT://:9093
    log.dir=/tmp/kafka-logs-1

config/server-2.properties:
    broker.id=2
    listeners=PLAINTEXT://:9094
    log.dir=/tmp/kafka-logs-2
```
### Kafka使用ZooKeeper的，所以你需要先启动ZooKeeper的服务器
```sh
bin/zookeeper-server-start.sh config/zookeeper.properties 1>/dev/null 2>&1 &
```
### 启动Kafka服务器 3个实例
```sh
bin/kafka-server-start.sh config/server.properties 1>/dev/null 2>&1 &
bin/kafka-server-start.sh config/server-1.properties 1>/dev/null 2>&1 &
bin/kafka-server-start.sh config/server-2.properties 1>/dev/null 2>&1 &
ps aux | grep server-1.properties
kill -9 pid # 杀进程
```

### 新增Topic test
```sh
bin/kafka-topics.sh --create --zookeeper localhost:2181 --replication-factor 1 --partitions 1 --topic test
bin/kafka-topics.sh --list --zookeeper localhost:2181 # 要获取Kafka服务器中的主题列表
bin/kafka-console-producer.sh --broker-list localhost:9092 --topic test # 发送消息
```

### 打开新终端，在kafka安装目录下执行如下命令，生成对topic test 的消费
```sh
sh bin/kafka-console-consumer.sh --bootstrap-server localhost:9092 --topic log --from-beginning 
```

## Kafka php [php-rdkafka](https://github.com/arnaud-lb/php-rdkafka)


```sh
# 安装librdkafka 
$ git clone https://github.com/edenhill/librdkafka.git
$ cd librdkafka
$ ./configure
$ make && make install
# 安装 php-rdkafka
git clone https://github.com/arnaud-lb/php-rdkafka.git
cd php-rdkafka
phpize
 ./configure
make all -j 5
sudo make install
php -i | grep rdkafka
```

## Flume+kafka 整合

### kafka.properties
```sh
agent.sources = s1
agent.channels = c1                                                                                    
agent.sinks = k1

agent.sources.s1.type=exec                                                    
agent.sources.s1.command=tail -F /var/log/nginx/access-api.fshd.com.log                    
agent.sources.s1.channels=c1   

agent.sources.s1.interceptors = i2
agent.sources.s1.interceptors.i2.type=org.apache.flume.sink.solr.morphline.UUIDInterceptor$Builder
agent.sources.s1.interceptors.i2.headerName=key
agent.sources.s1.interceptors.i2.preserveExisting=false

agent.channels.c1.type=memory
agent.channels.c1.capacity=10000                                                 
agent.channels.c1.transactionCapacity=100
                                                                                                                                      
#设置Kafka接收器
agent.sinks.k1.type= org.apache.flume.sink.kafka.KafkaSink                                                      
#设置Kafka的broker地址和端口号
agent.sinks.k1.brokerList=39.107.158.137:9092
#设置Kafka的Topic
agent.sinks.k1.topic=log
#设置序列化方式
agent.sinks.k1.serializer.class=kafka.serializer.StringEncoder                                                                                                                                  
agent.sinks.k1.channel=c1
```



