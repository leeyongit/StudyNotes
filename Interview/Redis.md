
### memcache和redis的内存分配原理
Memcache采用slab table的方式分配内存，首先把可得的内存按照不同的大小来分类，在使用时根据需求找到最接近于需求大小的块分配，来减少内存碎片，但是这需要进行合理配置才能达到效果。

Redis为了屏蔽不同平台之间的差异及统计内存占用量等，对内存分配函数进行了一层封装，在程序中统一使用zmalloc、zfree系列函数，这些函数位于zmalloc.h/zmalloc.c文件中。封装就是为了屏蔽底层平台的差异，同时方便自己实现相关的统计函数。具体的实现方式如下：
- 若系统中存在Google的TC_MALLOC库，则使用tc_malloc一族的函数代替原本的malloc一族的函数。
- 若当前系统是Mac系统，则使用系统的内存分配函数。
- 对于其它情况，在每一段分配好的空间前面同时多分配一个定长的字段，用来记录分配的空间大小，通过这种方式来实现简单有效的内存分配。


### memcache和redis的数据淘汰策略
Redis提供了丰富的淘汰策略，包括maxmemory、maxmemory-policy、volatile-lru、allkeys-lru、volatile-random、allkeys-random、volatile-ttl、noeviction(return error)等。

Memecache在容量达到指定值后，就基于LRU（Least Recently Used）算法自动删除不使用的缓存。在某些情况下LRU机制反倒会带来麻烦，会将不期待的数据从内存中清除，在这种情况下启动Memcache时，可以通过“M”参数禁止LRU算法。

### memcache的一致性哈希算法
先构造一个长度为2^32的整数环（这个环被称为一致性Hash环），根据节点名称的Hash值（其分布为[0, 2^32-1]）将服务器节点放置在这个Hash环上，然后根据数据的Key值计算得到其Hash值（其分布也为[0, 2^32-1]），接着在Hash环上顺时针查找距离这个Key值的Hash值最近的服务器节点，完成Key到服务器的映射查找。
> crc32() 函数计算字符串的 32 位 CRC（循环冗余校验）。该函数可用于验证数据完整性。

```php
$str = crc32("Shanghai");
printf("%u\n",$str);
```
>提示：为了确保从 crc32() 函数中获得正确的字符串表示，您需要使用 printf() 或 sprintf() 函数的 %u 格式符。如果未使用 %u 格式符，结果可能会显示为不正确的数字或者负数。
