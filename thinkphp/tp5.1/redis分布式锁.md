# 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 模拟并发第三方博客 | [link](https://www.jianshu.com/p/a41e41834d88)               |
| 分布式锁第三方博客 | [link](https://segmentfault.com/a/1190000019138071) [link](https://learnku.com/articles/42440) [link](https://www.yanghaihua.com/content/2020-08-09/1098.shtml) |
| 第三方库           | [link](https://github.com/ar414-com/php-redis-lock)          |
| reidis-set命令详解 | [link](http://doc.redisfans.com/string/set.html)             |

## 为什么需要锁

> 需要保护共享资源正常使用，不出乱子。
> 比方说，公司只在一间厕所，这是个其享资源，大家需要其同使用这个厕所，所以避免不了有时候会发生竞争。如果一个人正在使用，另外一个人进去了，昨办呢？如果两个人同时钻进了一个厕所，那该怎么办？结果如何？谁先用，还是一起使用？特别的，假如是一男一女同时钻进了厕所，事情会怎样呢？……
> 如果这个时候厕所门前有个锁，每个人都没法随便进入，而是需要先得到锁，才能进去。
> 而得到这个锁，就需要里边的人先出来。这样就可以保证同一时刻，只有一个人在使用厕所，这个人在上厕所的期间不会有不安全的事情发生，不会中途被人刚进来了。

## 分布式锁的基本条件

> 首先，为了确保分布式锁可用，我们至少要确保锁的实现同时满足以下几个条件：
> 1、互斥性。在任意时刻，只有一个客户端能持有锁。
> 2、不会发牛死锁。即使有一个客户端在持有锁的期间崩溃而没有丰动解锁，也能保证后续其他客户端能加锁。
> 3、解铃还须系铃人。加锁和解锁必须是同一个客户端，客户端自己不能把别人加的锁给解了，即不能误解锁。

# 模拟并发

### 模拟并发一

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

**查询是否有重复的列**

```mysql
SELECT COALESCE
	( count_decrement_id, '总数' ) count_decrement_id,
	COUNT( count_decrement_id ) AS id 
FROM
	`decrement_count` 
GROUP BY
	count_decrement_id WITH ROLLUP 
HAVING
	id > 1
```



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

### 模拟并发二



```php
if ($this->_redis->get('num') < 1) {
     sleep(5);
     Log::info('123');
     return "true";
 } else {
     return "false";
}
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





## 自己的实战

**分布式锁扩展类**

```php
<?php


namespace app\admin\controller;


class Lock
{
    protected $redis;
    protected $lockId; //记录加锁的客户端的id

    public function __construct($redis)
    {
        $this->redis = $redis;
    }

    /**
     * 加锁
     * @param string $scene 业务场景
     * @param int $expire 锁过期时间
     * @param int $retry 等待尝试时间
     * @param int $sleep 等待时间
     * @return false
     */
    public function lock($scene = "seckill", $expire = 5, $retry = 10, $sleep = 10000)
    {
        //同一个时刻只能有一个用户持有锁,并且不能出现死锁
        $res = false;
        while ($retry-- > 0) { # 尝试次数
            $value = session_create_id();//生成不重复的字符串(唯一的值)
           $res= $this->redis->set($scene, $value, 'NX', 'EX', $expire);
            //$res = $this->redis->setnx($scene, $value); //上锁成功修改返回结果
            #$res = $this->redis->set($scene, $value,['NX','EX'=>$expire]); //上锁成功修改返回结果
            var_dump('requestKey=' . $value);
            if ($res) {
                $this->lockId[$scene] = $value; //记录当前请求的key
                //$res->redis->expire($scene, $expire + 5);
                //加锁成功了
                break;//跳出
            }
            echo "尝试获取锁" . PHP_EOL;
            usleep($sleep);//睡眠
        }
        return $res;
    }

    /**
     * 删除锁
     * @param $scene
     * @return mixed
     */
    public function unLock($scene)
    {
        //能够删除自己的锁，而不应该删除别人的锁
        $id = $this->lockId[$scene];//当前请求记录value值
        if (isset($id)) {
            $value = $this->redis->get($scene); //先取出当前数据库当中记录的锁,从数据库当中取出来的
            var_dump('redisKey=' . $value, 'requestKey=' . $id);
            //从redis当中获取的id跟当前请求记录的id,是否是同一个
            if ($id == $value) {
                return $this->redis->del($scene);//删除锁
            }

        }
        return false;
    }
}

# 拓展 删除锁使用lua脚本实现原子化删除 

    /**
     * redis 嵌入lua优势
     * 减少网络开销：不使用Lua的代码需要向Redis发送多次请而脚本只需一次即可，减少网络传输
     * 原子操作：Redis将整个脚本作为一个原子执行，无需担心并发，也就无需事务；复用：脚本会永久保存Redis中，具他客户端可继续使用。
     * https://learnku.com/articles/15825/redis-distributed-lock-solution
     * 删除锁
     * @param $scene
     * @return mixed
     */
    public function unLock($scene)
    {
        $id = $this->lockId[$scene];//当前请求记录value值
        $script = <<<LUA
local key=KEYS[1]
local value=ARGV[1]
if(redis.call('get',key)==value)
then
return redis.call('del',key)
end
LUA;
        //redis中嵌入lua脚本
 return $this->redis->eval($script, 1, $scene, $id);
    }
```

**使用**

```php
<?php

namespace app\admin\controller;

use app\models\Count;
use app\models\DecrementCount;
use app\Traits\ApplyResponseLayout;

use Predis\Client;
use think\facade\Log;


class Demo
{
    use ApplyResponseLayout;

    private $_redis;

    public function __construct()
    {
        $this->_redis = new Client([
            'host' => 'redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com',
            'port' => 13822,
            'connectTimeout' => 2.5,
            'auth' => ['phpredis', 'phpredis'],
            'password' => 'yly274325132',
            'ssl' => ['verify_peer' => false],
        ]);
    }

    public function test()
    {
        $lock = new Lock($this->_redis);
        $scene = 'seckill';
        //如果加锁成功,某个业务只允许一个用户操作
        if ($lock->lock($scene, 5)) {//加锁
            var_dump("执行业务逻辑");
            $countModel = Count::where('id', 1)->find();
            $count = $countModel->value('count');
            if ($count > 0) {
                $bool = DecrementCount::create(['count_decrement_id' => $count--]);
                if ($bool) {
                    --$countModel->count;
                    $countModel->save();
                    $lock->unLock($scene);
                }
            }
        }
    }
}
```

