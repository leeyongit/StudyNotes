Redis笔记
--------

#### Redis--各个数据类型最大存储量
原文地址：https://redis.io/topics/data-types
- Strings类型：一个String类型的value最大可以存储512M
- Lists类型：list的元素个数最多为2^32-1个，也就是4294967295个。
- Sets类型：元素个数最多为2^32-1个，也就是4294967295个。
- Hashes类型：键值对个数最多为2^32-1个，也就是4294967295个。
- Sorted sets类型：跟Sets类型相似。