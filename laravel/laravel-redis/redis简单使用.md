# 一、安装

## 1.1 `redis`安装可以参考本人以前写的[博客](https://www.cnblogs.com/yaoliuyang/p/13197453.html)

## 1.2 `redis`可视化工具

> 这里推荐使用`phpstorm`自带的`RedisClient`

![Client](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210530200809761.png?versionId=CAEQEBiBgIDPw9n0zRciIGFjNmMwOTdiZWQxMzRlYWY4ZDAzYzNlOTY5MDE0ZDBh)

## 1.3 参考资料

| 名称                       | 地址                                                         |
| -------------------------- | ------------------------------------------------------------ |
| laravel学院高性能redis实战 | [链接](https://laravelacademy.org/books/high-performance-redis) |
| 第三方博客                 | [链接](http://blog.ganyongmeng.com/?p=99)                    |

# 二、使用

## 2.1 实例化`redis`

- 连接redis;

```php
use Illuminate\Support\Facades\Redis; 
$redis = Redis::connection(); # 如果还没有提示请这样 $redis=Redis::connection()->client();
```

## 2.2 经常使用的方法

```php
# 自增 increment
$redis->incr('likeMeCount');# 调用函数likeMeCount加1
$redis->incrby('age',10); #制定自增的数值incrby,年龄每次自增10 
# 自减
$redis->decr('likeMeCount');#调用函数likeMeCount减1
$redis->decrBy('likeMeCount',10)#调用函数likeMeCount减10
# 持久化保存值
$redis->save();# 在链式调用之后使用,将需要保存的信息持久化保存在内存中
# 删除值 
$redis->del('likeMeCount');#例如删除likeMeCount对象
# 字符串key-value
$redis->set('name','姚留洋');#保存一个变量name,值为自己的姓名
# 在元素后面追加值
$redis->append('name','666');#及结果为:姚留洋66
# 判断Key(变量)是否存在
$redis->exists('name');# 及获取Key(变量)name是否存在
#获取key的类型
$redis->type('name');#结果为string
# 获取字符串
$redis->get('name');#获取key为name的变量的值
# 获取字符串的长度
$redis->strlen('name');#结果(姚留洋)为9 因为string类型一个字符占用三个字节,所以结果为9
# 插入list类型数据 
$redis->lpush('names',['李四']);# 从左侧插入每次插入的值在数据的最顶层 ASC 正叙
$redis->rpush('names',['凌华']);# 从右侧插入每次插入的值在数据的最底层  DESC 倒叙
$redis->lrange('names',0,-1);# 获取list数据类型的全部值(获取列表元素)
$redis->llen('names');#获取列表的长度 及列表有多少条记录
# Setbit 命令用于对 key 所储存的字符串值，设置或清除指定偏移量上的位(bit)
# 此命令中间存储的数据只会存在一次不会重复存在
$redis->setbit('version:' . $userId . $request->input('id'), $userId, 1); #设置已阅读的版本号
$redis->getbit('version:' . $userId . $request->input('id'), $userId) #d
```



