#  





# 说明

> 以前有一个bug就是说用户签到如果用户手速过快于点击则会请求多次url，如果
>
> 其中包含派发积分等总要操作则会产生越权行为

第一版在中间件中使用 

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

第二种方案(此方案果断放弃,经过实验可以用但是接口延迟很大)

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
        if ($redis->get("request:{$hashKey}")<=1) {
            return $next($request);
        }else{
            sleep(1);
            return $next($request);
        }
```

