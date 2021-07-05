# 说明[文章搬取地址](https://mp.weixin.qq.com/s/1fpXbA6b8L5j4XRnOT45Ow)

## Laravel结合Redis实现黑名单、倒计时、防刷功能

新建的网站,如何限制别人恶意攻击、频繁请求接口,导致数据库崩溃？我们可以使用Redis对请求的IP做一个简单的限制。

#  一、设计思路

1、Redis中使用有序set表存放黑名单列表、频繁请求列表。

2、用户访问,设置一个锁,数值为1,过期时间10秒。

3、用户每次请求接口1次,锁的数值加1。在10秒内接口访问次数超过20次,则把该用户IP或uid添加到频繁请求列表中,score的值为当前时间，数据库表频繁请求次加1。

4、若频繁请求次数超过设定次数,则添加到redis黑名单列表中。

#  二、前期准备

1、在app\http\common中创建RedisKey.php

```php
p<?php

namespace App\Http\Common;

class RedisKey
{
    public static $USER_BLACK_LIST = "user:black:list";//黑名单列表
    public static $USER_FREQUENT_REQUEST_LIST = "user:frequent:request:list";//频繁请求列表
    public static $USER_FREQUENT_REQUEST_LOCK = "user:frequent:request:lock";//锁
}
```

2、创建数据库表

```sql
CREATE TABLE `blacklist` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `ip` varchar(255) DEFAULT NULL COMMENT '当前ip',
  `frequent num` int(11) DEFAULT NULL COMMENT '异常访问次数',
  `black_list` tinyint(4) DEFAULT NULL COMMENT '黑名单',
  `updated_at` timestamp NULL DEFAULT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='黑名单';
```



user表

3、使用命令创建CheckRequest.php路由中间件

```php
php artisan make:middleware CheckRequest
```

#  三、实现代码

```php
<?php

namespace App\Http\Middleware;

use App\Http\Common\Code;
use App\Http\Common\RedisKey;
use App\Models\FrontUser;
use Closure;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Redis;

class CheckRequest
{
    /**
     * Handle an incoming request.
     *
     * @param Request $request
     * @param Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        $ip = request()->ip();
        //查看redis黑名单中是否存在该IP
        $isBlack = Redis::zscore(RedisKey::$USER_BLACK_LIST, $ip);
        if ($isBlack) {
            return ["code" => Code::$USER_BLACK, "msg" => "由于您近期异常请求过于频繁，已限制访问。如需取消限制，请联系管理员：邮箱1048672466@qq.com!"];
        }
        $user = new FrontUser();
        $isUser = $user->where(["ip" => $ip])->first();
        //数据库中已设置为黑名单或频繁请求数大于等于10
        if ($isUser) {
        //查看数据库表中频繁请求次数是否超过10次，是就把该用户列入黑名单并修改相关字段
            if ($isUser->black_list === 1 || $isUser->frequent_num >= 10) {
                $isUser->black_list = 1;
                $isUser->save();
                Redis::zadd(RedisKey::$USER_BLACK_LIST, 1, $isUser->ip);
                return ["code" => Code::$USER_BLACK, "msg" => "由于您近期异常请求过于频繁，已限制访问。如需取消限制，请联系管理员：邮箱1048672466@qq.com!"];
            }
        }
        $time = 5;//锁过期时间
        $limit = 20;//5秒内请求次数，超过就触发防刷机制
        $lock_time = 60;//每次防刷的锁60秒
        //redis频繁请求列表
        $start_time = Redis::zscore(RedisKey::$USER_FREQUENT_REQUEST_LIST, $ip);
        //如果redis频繁请求列表中存在该用户IP，且间隔当前时间少于60秒
        if (time() - $start_time < $lock_time) {
            //返回剩余时间
            return response(["code" => Code::$USER_FREQUENT, "msg" => "频繁请求！", "data" => $lock_time - (time() - $start_time)]);
        }
        $frequentLock = RedisKey::$USER_FREQUENT_REQUEST_LOCK . ":" . $ip;
        $isFrequent = Redis::get($frequentLock);
        if (!$isFrequent) {
            Redis::setex($frequentLock, $time, 1); //设置锁
            return $next($request);
        }
        Redis::incr($frequentLock);//锁过期时间类数值自增
        //设定时间内请求次数大于设定次数，触发防刷机制
        if ($isFrequent > $limit) {
            Redis::zadd(RedisKey::$USER_FREQUENT_REQUEST_LIST, time(), $ip);
            Redis::expire(RedisKey::$USER_FREQUENT_REQUEST_LIST, 60 * 5);
            $isUser->increment("frequent_num");
            $isUser->save();
        }
        return $next($request);
    }
}
```