# 说明



laravel-学院[redis实战参考](https://laravelacademy.org/books/high-performance-redis)

有空待研究[redis-json](https://oss.redis.com/redisjson/)

[云redis数据库](https://app.redislabs.com/#/subscriptions/subscription/1703229/bdb)



# [消息订阅与发布](https://learnku.com/docs/laravel/8.x/redis/9405#08edd9)

**资料**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/weixin_43888891/article/details/130951434) |

**说明**

> **缺点**
>
> 订阅消息不能持久化

**创建订阅**

```php
# php artisan make:command RedisSubscribe
    
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Redis;

class RedisSubscribe extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'redis:subscribe';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Subscribe to a Redis channel';

    /**
     * Create a new command instance.
     *
     * @return void
     */
    public function __construct()
    {
        parent::__construct();
    }

    /**
     * Execute the console command.
     *
     * @return int
     */
    public function handle()
    {
        $redis = Redis::connection();
        $redis->subscribe(['test-channel'], function ($message) {
             echo $message;
        });
    }
}    
```

**测试订阅类**

```php
 public function test(Request $request)
    {
        $redis=Redis::connection()->client();
        $redis->publish('test-channel', json_encode(['foo' => 'bar']));
    }
```

# 实现接口防刷

```php
public function test(Request $request)
{

   return self::ipRequestLimit('test',$request->ip());
}
```






```php
/**
 * @param string $methodName 方法名称
 * @param string $ip 请求ip地址
 * @param float|int $timeout  过期时间
 * @param int $limit 限制请求次数
 * @return string
 */
public static function ipRequestLimit($methodName, $ip, $timeout = 10*60,$limit=2)
{
    $key = "ip_request_limit" . ":{$methodName}:" . $ip;
    $redis=RedisService::getInstance();
    $redis->expire($key, $timeout);// 当请求开始的时候即刻设置请求时间
    $redis->incr($key);//开始自增
    if ($redis->get($key)>=$limit) return  false;
    return true;
}
```

# 实现登陆失败次数限制

> 今天难的悠闲扒一扒用户登录失败次数限定，
>
> 思路： 采用`redis`的自增+过期时间实验业务逻辑
>
> 1. 如果用户登录失败则自增，用户没有登录机会的时候设置过期时间
> 2. 登录成功则取消失败登录次数



## 代码示例

```php
 use Illuminate\Support\Facades\Redis;
 public function login(){
   $redis = Redis::connection()->client();
   # 登录失败逻辑
   if(登录成功){
     $redis->del('user:' . $request->email);#如果登录成功则删除登录失败次数计数
   }
   if(登录失败){
            $limit = 5;# 失败次数最大值(可配置化)
            $time = 60 * 5;# 设置5分钟之后过期
            $count = $redis->get('user:' . $request->email);
            if ($count < $limit) {
                $redis->incr('user:' . $request->email);
            }
            $errorCount = $limit-$redis->get('user:' . $request->email);//还剩多少次机会
            if ($errorCount == 0 && $redis->ttl('user:' . $request->email) == -1) { # 失败次数等于0，并且没有设置过期时间的时候才设置过期时间
                $redis->expire('user:' . $request->email, $time);# 如果等于0,并且过期时间不存在,设置过期时间,ttl:秒
            }
            $expirationTime = $this->getSerializeTime(floor($redis->pttl('user:' . $request->email) / 1000));# 查看还有多少时间过期
            $message = $errorCount == 0 ? "登陆失败,您还有{$errorCount}次机会请{$expirationTime}后再尝试" : 
                                          "登陆失败,您还有{$errorCount}次机会";
            return ResponseLayout::apply(false, $message);
       }
   }
    # 构建时间
    public function getSerializeTime($time)
    {
        if ($time != -1) {
            $serializeTime = $time;# 传输过来的时间戳
            $oneMinuteTimestamp = 60;# 一分钟的时间戳
            $oneHourTimestamp = 60 * 60;# 一小时的时间戳
            $oneDayTimestamp = 60 * 60 * 24;# 一天的时间戳
            $oneMonthTimestamp = 60 * 60 * 24 * 30;# 一个月的时间戳
            $oneYearTimestamp = 60 * 60 * 24 * 365;# 一年的时间戳
            if ($serializeTime < $oneMinuteTimestamp) {
                return $serializeTime . '秒';
            } elseif ($serializeTime < $oneHourTimestamp) {
                return floor($serializeTime / 60) . '分';
            } elseif ($serializeTime < $oneDayTimestamp) {
                return floor($serializeTime / (60 * 60)) . '小时';
            } elseif ($serializeTime < $oneMonthTimestamp) {
                return floor($serializeTime / (60 * 60 * 24)) . '天';
            } elseif ($serializeTime < $oneYearTimestamp) {
                return floor($serializeTime / (60 * 60 * 24 * 30)) . '月';
            } else {
                # 如果大于一年直接返回时间
                return date('Y-m-d H:i:s', strtotime($time));
            }
        }
    }
```

# redis限制多次请求只计算一次

## 说明

> 以前有一个bug就是说用户签到如果用户手速过快于点击则会请求多次url，如果
>
> 其中包含派发积分等总要操作则会产生越权行为

## 第一版在中间件中使用 

> 此方案在签到等只能请求一次的方式上很好的实现解决
>
> 但是如果是那种点赞 可以取消点赞&再次点赞的操作此方案就
>
> 不适用了

```php
  public function handle($request, Closure $next)
    {
        $user_token = $request->header('xcx-token');//token
        $method=$request->getMethod();//请求方法
        $path=$request->path();//路径
        $params=$request->all();
        $hashKey=md5(json_encode(compact('user_token','method','path','params')));
        $limit = 1;// 最大请求次数(可配置化)
        $time = 10;// 过期时间秒
        $redis = Redis::connection()->client(); // 实例化redis
        // 如果ip不存在的情况下才会创建
        if ($redis->get("request:{$hashKey}") == null) {
            $redis->set("request:{$hashKey}", $limit);//设置请求的ip与最多访问次数
            $redis->expire("request:{$hashKey}", $time);// 当请求开始的时候即刻设置请求时间
        }
        $expirationTime = floor($redis->pttl("request:{$hashKey}") / 1000);//过期时间
        if ($redis->get("request:{$hashKey}") == 0 && $expirationTime != 0) {
            return $this->error('请勿重复请求');
        }
        if ($redis->get("request:{$hashKey}") != 0) {
            $redis->decr("request:{$hashKey}");// 请求即自减
        }
        return $next($request);
    }
```

## 第二种方案(此方案果断放弃,经过实验可以用但是接口延迟很大)

> 此次想的是如果用户特定时间内比如5秒多次获1此点击
>
> 请求api的时候只通过第一次或则最后一次请求
>
> 防抖功能 ：
>
> 防抖就是将多次高频操作优化为只在最后一次执行（某个函数在某段时间内，无论触发了多少次回调，都只执行最后一次）。通常的使用场景是：用户输入，只需在输入完成后做一次输入校验即可。

```shell
        $user_token = $request->header('xcx-token');//token
        $method = $request->getMethod();//请求方法
        $path = $request->path();//路径
        $params = $request->all();
        $hashKey = md5(json_encode(compact('user_token', 'method', 'path', 'params')));
        $time = 5;// 过期时间秒
        $redis = Redis::connection()->client(); // 实例化redis
        // 如果ip不存在的情况下才会创建
        $redis->incr("request:{$hashKey}");
        $redis->expire("request:{$hashKey}", $time);// 当请求开始的时候即刻设置请求时间
        if ($redis->get("request:{$hashKey}")<=1) return $next($request);
        sleep(1);
        return $next($request);
        
```

# reids 实现统计页面日点击量(pv uv)统计

参考 https://www.jianshu.com/p/3ea95c0fc814

## 使用

```php
$redis = new Client();
$date=date('Ymd');# 定义存储的名称
$redis->setbit($date,user_id,1);#  第二个参数是用户的id
dd($redis->bitcount($date));# 得到统计的个数
```

## 使用sadd函数实现

> Redis Sadd 命令将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。
>
> 假如集合 key 不存在，则创建一个只包含添加的元素作成员的集合。
>
> 当集合 key 不是集合类型时，返回一个错误。
>
> **注意：**在Redis2.4版本以前， SADD 只接受单个成员值。

```php
# 核心二：使用redis的sAdd()方法
/**
 * sadd()命令将一个或多个成员元素加入到集合中，已经存在于集合的成员元素将被忽略。
 * 查询sadd()添加的个数sCard()
 **/
# 控制器代码示例
  public function setPvAndUv(Request $request)
    {
        $store_id = $request->input('store_id');
        if ($store_id == null) {
            throw new ErrorCodeException('商店主键不能为空');
        }
        $today = date('Ymd');
        $pvKey = "tw:pv:$store_id:$today"; //pvKey
        $uvKey = "tw:uv:$store_id:$today"; //uvKey
        $expiration_time = 3600 * 24 * 2;//过期时间2天
        //pv
        $isCrPv = $this->redis->incr($pvKey);
        $this->redis->expire($pvKey, $expiration_time);//设置redis过期时间
        //uv
        $isCrUv=$this->redis->sAdd($uvKey,56456);//参数2 user_id由于目前不知道此参数的具体位置所以写死代替
        $this->redis->expire($uvKey, $expiration_time);//设置redis过期时间
        return compact('isCrPv','isCrUv');
    }
# 自动同步代码示例
 public function __construct()
  {
        parent::__construct();
        $this->redis = Redis::connection()->client();
  }
   public function handle()
    {
        $store = Store::get(['store_id']);
        $date = date('Ymd');
        //构建昨天的时间戳
        $yesterday = strtotime(date('Y-m-d')) - 86400;
        $yesterday = date('Y-m-d', $yesterday);
        foreach ($store as $value) {
            $pvKey = "tw:pv:$value->store_id:$date"; //pvKey
            $uvKey = "tw:uv:$value->store_id:$date"; //uvKey
            //查询是本商店并且日期为昨天的数据
            $store = StoreOrderStatistical::firstOrNew(['store_id' => $value->store_id, 'date' => $yesterday]);
            $store->store_id = $value->store_id;
            $store->date = $yesterday;//昨天的数据
            $store->pv = $this->redis->get($pvKey) ?? 0;
            $store->uv = $this->redis->sCard($uvKey) ?? 0;
            $store->save();
        }
    }
```

