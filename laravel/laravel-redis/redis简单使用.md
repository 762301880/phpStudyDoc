# 一、安装

## 1.1 `redis`安装可以参考本人以前写的[博客](https://www.cnblogs.com/yaoliuyang/p/13197453.html)

## 1.2 `redis`可视化工具

> 这里推荐使用`phpstorm`自带的`RedisClient`

![Client](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210530200809761.png?versionId=CAEQEBiBgIDPw9n0zRciIGFjNmMwOTdiZWQxMzRlYWY4ZDAzYzNlOTY5MDE0ZDBh)

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
# 自增 increment
$redis->incr('likeMeCount');# 调用函数likeMeCount加1
# 自减
$redis->decr('likeMeCount');#调用函数likeMeCount减1
# 持久化保存对象
$redis->save();# 在链式调用之后使用,将需要保存的信息持久化保存在内存中
# 删除对象 
$redis->del('likeMeCount');#例如删除likeMeCount对象
```



