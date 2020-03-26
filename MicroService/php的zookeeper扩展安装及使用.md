php的zookeeper扩展安装及使用
=========
ZooKeeper是一个分布式的，开放源码的分布式应用程序协调服务，是Google的Chubby一个开源的实现，是Hadoop和Hbase的重要组件。它是一个为分布式应用提供一致性服务的软件，提供的功能包括：配置维护、域名服务、分布式同步、组服务等。

ZooKeeper的目标就是封装好复杂易出错的关键服务，将简单易用的接口和性能高效、功能稳定的系统提供给用户。

要在php中使用zookeeper，先要安装php zookeeper扩展，要安装php zookeeper扩展，得先安装zookeeper

```
$ sudo yum update -y
```
OpenJDK
选用JDK8最新版

安装
```
$ sudo yum install -y java-1.8.0-openjdk-devel
```
安装完成
```
$ java -version
```
1. 下载
```
$ cd /opt
$ wget http://mirrors.hust.edu.cn/apache/zookeeper/zookeeper-3.4.13/zookeeper-3.4.13.tar.gz
```
2. 解压
```
$ tar zxvf zookeeper-3.4.13.tar.gz
mv zookeeper-3.4.13 zookeeper
```
3. 生成配置文件
```
cd zookeeper/
$ cp conf/zoo_sample.cfg conf/zoo.cfg
```
4. 启动
```
$ bin/zkServer.sh start
```
停止
```
$ bin/zkServer.sh stop
```
重启
```
$ bin/zkServer.sh restart
```

## 安装libzookeeper
cd src/c
./configure --prefix=/usr/local/zookeeper-lib/
make && make install

## 安装php-zookeeper
wget https://pecl.php.net/get/zookeeper-0.6.4.tgz
tar -zxvf zookeeper-0.6.4.tgz
cd zookeeper-0.6.4
phpize
./configure --with-php-config=/usr/bin/php-config --with-libzookeeper-dir=/usr/local/zookeeper-lib/
make && make install
extension=zookeeper.so

```php
// 获取单条配置
$path = '/' . trim($path, '/');
$zookeeper = new \Zookeeper('localhost:2181');
if ($zookeeper->exists($path)) {
    $value = $zookeeper->get($path);
}
//创建节点
if (!$zookeeper->exists($path)) {
    $zookeeper->create($path, $value, array(array(
        'perms' => 31,
        'scheme' => 'world',
        'id' => 'anyone'
    )));
}
//更新节点
if ($zookeeper->exists($path)) {
    $zookeeper->set($path, $value);
}
//删除一个节点
if ($zookeeper->exists($path)) {
    $zookeeper->delete($path);
}
//获取所有子节点名称
if ($zookeeper->exists($path)) {
    $value = $zookeeper->getChildren($path);
}
//获取acl
if ($zookeeper->exists($path)) {
    $zookeeper->getAcl($path);
}
//设置acl
if ($zookeeper->exists($path)) {
    $acl = $zookeeper->getAcl($path);
    $version = isset($acl['0']['aversion']) ? $acl['0']['aversion'] : 0;
    $zookeeper->setAcl($path, $version, array(array(
        'perms' => 31,
        'scheme' => 'world',
        'id' => 'anyone'
    )));
}
```
