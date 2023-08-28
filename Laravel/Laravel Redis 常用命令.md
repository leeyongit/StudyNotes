# Laravel Redis 常用命令

在 Laravel 中使用 Redis 极其的方便，你只需要将 preids/predis 通过composer 安装到 Laravel 的项目中即可：
```sh
composer require predis/predis
```
这样在使用默认的设置下，如果你的 redis 服务有正常启动，那么这样你就可以直接在 Laravel 项目中使用 Redis 了。

一开始，我们假设想设置下面的数据：
```php
Redis::set('product:1:sales', 1000)
Redis::set('product:1:count', 10)
```
所以，我们想获取其中某个值的时候，可以直接这样：
```php
Redis::get('product:1:sales')
```
Redis 增加和减少
```php
Redis::incr('product:1:count')
Redis::incrby('product:1:sales', 100)
```
默认情况下 incr 的增量为 1，如果增量不是 1 的话，可以通过第二锅参数指定。同理，减少也是一样的：
```php
Redis::decr('product:1:count')
Redis::decrby('product:1:sales', 100)
```
用法一模一样，只不过方法的名字变成 decrby 了。

但是需要注意的是，如果是对浮点（float）数进行增加或者减少，用 incrbyfloat:
```php
Redis::incrbyfloat('product:1:sales', 15.5)
Redis::incrbyfloat('product:1:sales', - 30.2)
```
遗憾的是，并没有 decrbyfloat 这个方法来做减法，而是使用 incrbyfloat 的第二个参数传入负值的情况下实现减少。

查找的同时更新
```php
$value = Redis::getset('product:1:sales', 0)
```
如上，我们获取到 product:1:sales 的值（应该是 1000），并且在获取之后将该值设置为 0。

指定过期时间
```php
Redis::set('user:1:notified', 1, 'EX', 3600);
```
这样的情况下，user:1:notified 就会在 3600 秒之后过期。这个应用场景我觉得是针对短信验证码这样的需求最合适了。

如果你想指定哎某个时间点过期，你可以这样使用 expireat 方法：
```php
Redis::expireat('user:1:notified', '1495469730')
```
注意第二个参数传入的是时间戳，未来的某个时间点。

是否存在某个 Key
想象这样一个场景，我们做了一个卖票的应用，一个人只能买一张票，如果票售出的话，我们就关闭该用户的购买渠道：
```php
Redis::set('ticket:sold', $user->id, 'NX')
```
这样的情况下，ticket:sold 只会在他原本不存在的情况下去设置这个值。如果你想判断一个 Key 是否存在，可以这样：
```php
Redis::exists('ticket:sold')
```
一次读取多个值
很简单啦！
```php
Redis::mget('product:1:sales', 'product:2:sales', 'non_existing_key')
```
这样会遇到一个问题就是：如果 key 不存在的话， Redis 怎么处理？

答：不存在的 key 返回 null

删除 Key
直接使用 del 方法：
```php
Redis::del('previous:sales', 'previous:return');
```
修改 Key 名字
```php
Redis::rename('current:sales', 'previous:sales');
```
但是这个重新命名的话，又会存在一个问题就是：万一新名字已经存在了呢（例子中的 previous:sales），这个时候可以直接：
```php
Redis::renamenx('current:sales', 'previous:sales');
```
也就是从 rename 方法变为 renamex 方法。

总结
在 Laravel 中使用 Redis 的时候，其实很多方法都是可以直接猜出来的，他们的命名跟 redis 的命令其实很像，比如我们这里的 incr，incrby，decr， decrby rename del 等。可以直接用！