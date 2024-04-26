#  实现接口防刷



```php
    public function test(Request $request)
    {

       return self::ipRequestLimit('test',$request->ip());
    }


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

