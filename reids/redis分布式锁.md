# 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 模拟并发第三方博客 | [link](https://www.jianshu.com/p/a41e41834d88) [link](https://learnku.com/articles/42440#06ef6c) |
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
        $countModel = Count::where('id', 1)->first();
        $count = $countModel->count;
        //sleep(1);   加上sleep 延迟并发效果更佳优秀
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

![1648709063(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/Ytce6Dp25ykZKO4.png)



![1648709150(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/YGbTep5c3o4Qawk.png)

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



#  分布式锁





![1648543719(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/7pwlNQmIhi4OsjY.png)





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
     * 参考资料
     * https://learnku.com/articles/15825/redis-distributed-lock-solution
     * https://learnku.com/laravel/search?q=php+redis+%E5%88%86%E5%B8%83%E5%BC%8F%E9%94%81
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
        
        
# 悲观锁-原始写法        
//        Db::startTrans();
//        $countModel = Count::where('id', 1)->lock(true)->find();
//        $count = $countModel->value('count');
//        if ($count > 0) {
//            $value = $count--;
//            $bool = DecrementCount::create(['count_decrement_id' => $value]);
//            if ($bool) {
//                --$countModel->count;
//                $countModel->save();
//            }
//        }
//        Db::commit();
        
        
        
        
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

# 使用redis事务实现秒杀

**参考代码**

```php
<?php

namespace App\Http\Controllers;


use Illuminate\Http\Request;
use Illuminate\Support\Facades\Redis;


class TestController extends Controller
{
    protected $redis;

    public function __construct()
    {
        $this->redis = Redis::connection()->client();
    }

    public function test(Request $request)
    {
        $this->redis->watch('sales');//乐观锁,监视作用
        $sales = $this->redis->get('sales');
        $store = 100;//总库存
        if ($sales >= $store) {
            exit('秒杀结束');
        }
        $this->redis->multi();//开启redis事务
        $this->redis->incr('sales');//销量+1
        $res = $this->redis->exec();//提交事务
        if ($res) {
            //成功
            //写库 更新库存操作
        }
        if (!$res) {
            //失败操作
            \Log::info('秒杀失败');
            exit('秒杀失败');
        }
    }
}

```

# laravel 内置的 `Cache` 锁（简单易用）

```php
<?php

namespace App\Http\Controllers;

use App\Models\Count;
use App\Models\DecrementCount;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;

class TestController extends Controller
{
    public function test(Request $request)
    {
        // 定义锁名称（唯一标识临界资源）
        $lockKey = "seckill:lock:1";
        // 初始化锁实例变量
        $lock = false;

        try {
            // 尝试获取锁：有效期10秒，等待2秒
            // 兼容两种情况：返回锁实例 或 返回false
            $lock = Cache::lock($lockKey, 10)->block(2);

            // 判断是否成功获取锁（核心修复点）
            if (!$lock) {
                return response()->json(['code' => 400, 'msg' => '请稍后再试（请求太频繁）']);
            }

            // 核心业务逻辑（临界区）
            $countModel = Count::where('id', 1)->first();
            if (!$countModel) {
                return response()->json(['code' => 404, 'msg' => '计数记录不存在']);
            }

            $count = $countModel->count;
            // sleep(1); // 测试并发时可开启

            if ($count > 0) {
                // 修正递减逻辑：先递减再赋值
                $newCount = $count - 1;
                $bool = DecrementCount::create(['count_decrement_id' => $newCount]);
                if ($bool) {
                    $countModel->count = $newCount;
                    $countModel->save();
                }
            }

            return response()->json(['code' => 200, 'msg' => '成功', 'data' => $count - ($bool ? 1 : 0)]);

        } catch (\Exception $e) {
            // 捕获所有异常（包括锁相关、数据库相关）
            return response()->json(['code' => 500, 'msg' => '系统错误:'.$e->getMessage()]);
        } finally {
            // 修复：只有锁是有效实例时，才释放锁
            if ($lock && method_exists($lock, 'release')) {
                $lock->release();
            }
        }
    }
}
```

# laravel redis分布式锁

```php
<?php

namespace App\Http\Controllers;

use App\Models\Count;
use App\Models\DecrementCount;
use App\Services\RedisService;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Cache;

class TestController extends Controller
{
    public function test(Request $request)
    {
        // 定义锁名称（唯一标识临界资源）
        $lockKey = "stock:lock:1";
        $lockValue = uniqid(); // 唯一值，防止误删其他请求的锁
        $expireSeconds = 10;
        // 1. 尝试获取锁：SETNX + 过期时间（原子操作，避免死锁）
        $redis=RedisService::getInstance();
        try {

            $acquired = $redis->set($lockKey, $lockValue, 'NX', 'EX', $expireSeconds);

            if (!$acquired) {
                return response()->json(['code' => 400, 'msg' => '操作中，请稍后']);
            }


            // 核心业务逻辑（临界区）
            $countModel = Count::where('id', 1)->first();
            if (!$countModel) {
                return response()->json(['code' => 404, 'msg' => '计数记录不存在']);
            }

            $count = $countModel->count;
            // sleep(1); // 测试并发时可开启

            if ($count > 0) {
                // 修正递减逻辑：先递减再赋值
                $newCount = $count - 1;
                $bool = DecrementCount::create(['count_decrement_id' => $newCount]);
                if ($bool) {
                    $countModel->count = $newCount;
                    $countModel->save();
                }
            }

            return response()->json(['code' => 200, 'msg' => '成功', 'data' => $count - ($bool ? 1 : 0)]);
        } finally {
            // 2. 释放锁：先判断value是否匹配，再删除（防止误删）
            $script = <<<LUA
            if redis.call('get', KEYS[1]) == ARGV[1] then
                return redis.call('del', KEYS[1])
            else
                return 0
            end
        LUA;
            $redis->eval($script, 1, $lockKey, $lockValue);
        }
    }
}
```

