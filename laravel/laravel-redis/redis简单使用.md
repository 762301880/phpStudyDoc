# 一、安装

## 1.1 `redis`安装可以参考本人以前写的[博客](https://www.cnblogs.com/yaoliuyang/p/13197453.html)

## 1.2 `redis`可视化工具

> 这里推荐使用`phpstorm`自带的`RedisClient`

![Client](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210530200809761.png?versionId=CAEQEBiBgIDPw9n0zRciIGFjNmMwOTdiZWQxMzRlYWY4ZDAzYzNlOTY5MDE0ZDBh)

## 1.3 参考资料

| 名称                       | 地址                                                         |
| -------------------------- | ------------------------------------------------------------ |
| laravel学院高性能redis实战 | [链接](https://laravelacademy.org/books/high-performance-redis) |
|                            |                                                              |

# 二、使用

## 2.1 实例化`redis`

- 第一种实例化方法

```php
use Predis\Client;
$redis = new Client();
```

- 第二种实例化方法

```php
use Illuminate\Support\Facades\Redis;
$redis = Redis::connection();
```

## 2.2 经常使用的方法

> 请实例化reids对象之后使用
>
> $redis = new Client();

```php
# 1.自增 increment
$redis->incr('likeMeCount');# 调用函数likeMeCount加1
# 2.自减
$redis->decr('likeMeCount');#调用函数likeMeCount减1
# 3.持久化保存值
$redis->save();# 在链式调用之后使用,将需要保存的信息持久化保存在内存中
# 4.删除值 
$redis->del('likeMeCount');#例如删除likeMeCount对象
# 5.字符串key-value
$redis->set('name','姚留洋');#保存一个变量name,值为自己的姓名
# 6.获取字符串
$redis->get('name');#获取key为name的变量的值
# 7.获取字符串的长度
$redis->strlen('name');#结果(姚留洋)为9 因为string类型一个字符占用三个字节,所以结果为9
# 8.插入list类型数据 
$redis->lpush('names',['李四']);# 从左侧插入每次插入的值在数据的最顶层 ASC 正叙
$redis->rpush('names',['凌华']);# 从右侧插入每次插入的值在数据的最底层  DESC 倒叙
$redis->lrange('names',1,-1);# 获取list数据类型的全部值
```



