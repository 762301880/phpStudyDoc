# 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 模拟并发第三方博客 | [link](https://www.jianshu.com/p/a41e41834d88)               |
| 分布式锁第三方博客 | [link](https://segmentfault.com/a/1190000019138071) [link](https://learnku.com/articles/42440) [link](https://www.yanghaihua.com/content/2020-08-09/1098.shtml) |
| 第三方库           | [link](https://github.com/ar414-com/php-redis-lock)          |

# 模拟并发

**创建数据库**

```mysql
# 数据库count 其中count 字段设置100 用于模拟,商品剩余数量
CREATE TABLE `count` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `count` int(11) DEFAULT NULL COMMENT '剩余数量',
  `create_time` datetime DEFAULT NULL COMMENT '创建时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8mb4;

# decrement_count  自建couent表 用于模拟对应创建订单数量(例如count表自减100 这里就有100条数据) 
CREATE TABLE `decrement_count` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `count_decrement_id` int(11) DEFAULT NULL, # 此字段用于表示count表剩余数量
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=401 DEFAULT CHARSET=utf8mb4 COMMENT='订单余数表';
```

**代码模拟并发**

```php
        $countModel = Count::where('id', 1)->find();
        $count = $countModel->value('count');
        if ($count > 0) {
            $bool = DecrementCount::create(['count_decrement_id' => $count--]);
            if ($bool) {
                --$countModel->count;
                $countModel->save();
            }
        }
```

**压测并发**

> 我们可以用压测工具压测 100 次会发现同时请求并发很严重 第一张表因该是0结果是47，第二张表结果应该是从100-1 虽然生成了100条数据但是
>
> 却基本上都是错误的并发

![1648709063(1).jpg](https://s2.loli.net/2022/03/31/Ytce6Dp25ykZKO4.png)



![1648709150(1).jpg](https://s2.loli.net/2022/03/31/YGbTep5c3o4Qawk.png)

**解决方案- mysql 悲观锁**

> 利用mysql的悲观锁实现解决并发，这里就不演示了亲测很多次都可以保持数据的一致性

```php
        # mysql悲观锁
        Db::startTrans();
        $countModel = Count::where('id', 1)->lock(true)->find();
        $count = $countModel->value('count');
        if ($count > 0) {
            $bool = DecrementCount::create(['count_decrement_id' => $count--]);
            if ($bool) {
                --$countModel->count;
                $countModel->save();
            }
        }
       Db::commit();
```



#  分布式锁





![1648543719(1).jpg](https://s2.loli.net/2022/03/29/7pwlNQmIhi4OsjY.png)





















# **别人写的代码分析**

```php
<?php

namespace Ar414;

class RedisLock
{
    private $redis;
    private $timeout = 3;

    public function __construct(\Redis $redis)  
    {
        $this->redis = $redis;    # 实例化这个类的时候传递redis实例过来
    }

    public function getLockCacheKey($key)
    {
        return "lock_{$key}";  # 设置key
    }

    public function getLock($key, $timeout = NULL)
    {
        $timeout = $timeout ? $timeout : $this->timeout; # 如果没有设置时间给一个默认时间
        $lockCacheKey = $this->getLockCacheKey($key); # 得到设置的key
        $expireAt = time() + $timeout; # 过期时间
        $isGet = (bool)$this->redis->setnx($lockCacheKey, $expireAt); # 设置值
        if ($isGet) { # 如果上锁成功直接返回上锁时间
            return $expireAt;
        }
        # 上锁失败进入循环 
        while (1) {
            usleep(10); # 延迟 10微妙  1000000 微秒 等于1秒
            $time = time();  
            $oldExpire = $this->redis->get($lockCacheKey); # 因为上一步的锁不会释放所以第一个人之后的总会走到这里，返回旧锁时间
            if ($oldExpire >= $time) { # 如果旧锁时间>=当前时间没有过期跳过进行下一次循环
                continue;
            }
            # 走到这里说明旧锁已经过期了
            $newExpire = $time + $timeout; # 设置一个新的过期时间 
            $expireAt = $this->redis->getset($lockCacheKey, $newExpire); # 返回旧值设置新值
            if ($oldExpire != $expireAt) { # 如果旧的值不等于旧的值跳过继续下一次循环
                continue;
            }
            $isGet = $newExpire;
            # 设置新的值并跳出循环
            break;
        }
        return $isGet; # 说明已经设置成功
    }

    public function releaseLock($key, $newExpire)
    {
        $lockCacheKey = $this->getLockCacheKey($key);
        if ($newExpire >= time()) {
            return $this->redis->del($lockCacheKey);
        }
        return true;
    }
}
# 使用实例
<?php

use Ar414\RedisLock;

$redis = new \Redis();
$redis->connect('127.0.0.1','6379');

$lockTimeOut = 5;
$redisLock = new RedisLock($redis,$lockTimeOut);

$lockKey    = 'lock:user:wallet:uid:1001';
$lockExpire = $redisLock->getLock($lockKey);

if($lockExpire) {
    try {
        //select user wallet balance for uid
        $userBalance = 100;
        //select goods price for goods_id
        $goodsPrice = 80;

        if($userBalance >= $goodsPrice) {
            $newUserBalance = $userBalance - $goodsPrice;
            //TODO set user balance in db
        }else {
            throw new Exception('user balance insufficient');
        }
        $redisLock->releaseLock($lockKey,$lockExpire);
    } catch (\Throwable $throwable) {
        $redisLock->releaseLock($lockKey,$lockExpire);
        throw new Exception('Busy network');
    }
}
```







