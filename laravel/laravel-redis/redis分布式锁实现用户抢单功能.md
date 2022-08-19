# 说明

> ***场景***
>
> 商品抢单，开发过程中有这么一种情况，如果一种优惠商品只放出了100个名额，但是
>
> 有10000+的用户来同时抢这100个订单，就会产生并法的情况,
>
> ***redis 分布式锁***
>
> 核心思想就是用户抢单的时候会对当前抢单操作进行加锁，如果我加锁成功别人就不可以进行抢单，
>
> 反之别人加了锁自己就不可以抢单,当自己加锁成功则进行下单操作，下单成功之后解锁，下一个
>
> 用户可以继续抢单，重复以上逻辑，直到订单抢完了为止

## 资料

| name                                                         | url                                                          |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| redis-setnx(**SET** if **N**ot e**X**ists)                   | [link](https://www.runoob.com/redis/strings-setnx.html)      |
| 第三方博客                                                   | [link](https://www.cnblogs.com/jackzhuo/p/13678008.html)  [link](https://learnku.com/articles/57086) |
| laravel学院-基于 Redis 实现分布式锁及其在 Laravel 底层的实现源码 | [link](https://laravelacademy.org/post/22183)                |
| 新版redis set命令实现分布式锁                                | [link](https://segmentfault.com/a/1190000019138071) [link](https://learnku.com/laravel/t/47073?order_by=created_at) |
| 小米信息部分布式锁参考                                       | [link](https://xiaomi-info.github.io/2019/12/17/redis-distributed-lock/) |

>***setnx解释***
>
>命令在指定的 key 不存在时，为 key 设置指定的值。
>
>### 返回值
>
>设置成功，返回 1 。 设置失败，返回 0 。

# 逻辑示例

```php
  $expire = 5;//过期时间
        $key = "lock:$request->input('order_id')";//key:订单id
        $value = time() + $expire;//锁的值 = Unix时间戳 + 锁的有效期
        $lock = $this->redis->setnx($key, $value);//设置分布式锁
        $isRob = false; //是否抢购成功
        //如果设置分布式锁成功或者分布式锁已过期
        if (!empty($lock) || $this->redis->get($key) <= time()) {
            # 逻辑操作
             $this->redis->del($key);
        }
```

# [laravel-学院 redis底层分布式锁示例](https://laravelacademy.org/post/22183)

# 自己研究的代码示例

### 说明

> 生活中并发的场景随处可见,抽奖，下单，签到，秒杀，都是那一瞬间并发量很大的情况
>
> 很可能会照成重复的情况,往往这种情况都是在读取剩余数量的时候判断那一瞬间很多人操作
>
> 会发生这种情况

### 模拟并发

> 举一个简单的**栗子**加入我们注册学生信息的时候不想要学生姓名重复,如果
>
> 重复则跳过执行,问题来了正常情况下我们是不会重复的 使用**jmeter**请求一下发现
>
> 会出现并发情况

```shell
       $name ='001';
        if (Stu::where('sname', $name)->first() == null) {
           Stu::create(['sname' => $name]);
        } 
```

![1640238629(1).jpg](https://s2.loli.net/2021/12/23/Pj7sGnJSYDO53IX.png)

### 利用mysql的悲观锁解决

> 记住悲观锁必须依赖于事务才可以执行

```shell
       $name ='001';
        DB::beginTransaction();
        if (Stu::where('sname', $name)->lockForUpdate()->first() == null) {
            Stu::create(['sname' => $name]);
            DB::commit();
        } else {
            DB::rollBack();
        }
```

# laravel 分布式锁

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
            $res = Redis::set('key', 'value', 'NX', 'EX', 10); //上锁成功修改返回结果
            #$res = $this->redis->set($scene, $value,['NX','EX'=>$expire]); //上锁成功修改返回结果
            var_dump('requestKey='.$value);
            if ($res) {
                $this->lockId[$scene] = $value; //记录当前请求的key
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
            var_dump('redisKey='.$value, 'requestKey='.$id);
            //从redis当中获取的id跟当前请求记录的id,是否是同一个
            if ($id == $value) {
                return $this->redis->del($scene);//删除锁
            }
        }
        return false;
    }
}
```

**使用示例**

```php
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
```

