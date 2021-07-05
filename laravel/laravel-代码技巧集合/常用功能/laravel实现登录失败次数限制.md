# 一、说明

> 今天难的悠闲扒一扒用户登录失败次数限定，
>
> 思路： 采用`redis`的自增+过期时间实验业务逻辑
>
> 1. 如果用户登录失败则自增，用户没有登录机会的时候设置过期时间
> 2. 登录成功则取消失败登录次数

# 二、代码示例 

```shell
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

