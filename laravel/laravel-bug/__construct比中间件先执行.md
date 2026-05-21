## 事情叙述

> 事情是这样的 今天我重写了 jwt 验证  从 中间件中验证

### 中间件逻辑

```php
public function handle(Request $request, Closure $next)
    {
        echo 'middleware'.PHP_EOL; # 打印执行顺序 middleware
        try {
            $authHeader = $request->header('Authorization');
            if (empty($authHeader) || $authHeader === 'Bearer null') return $this->error('token不存在');
            $payload = JWTAuth::parseToken()->getPayload();

            $userId = $payload->get('sub');
            $exp = $payload->get('exp');
            $expDate = date('Y-m-d H:i:s', $exp);
            $thisDate = date('Y-m-d H:i:s');
            if ($thisDate > $expDate) throw new ApiException('token已过期');
            $user = User::find($userId);
            $request->user = $user;
            $request->user_id = $userId;
        } catch (\Exception $exception) {
            $this->error($exception->getMessage());
        }
        return $next($request);
    }
```

### 控制器逻辑

```php
    protected $user_id = '';

    public function __construct()
    {
        echo '__construct'.PHP_EOL; #打印执行顺序__construct	
        $this->user_id = $request->user_id;
    }

    public function getUserId(){
       echo 'getUserId'.PHP_EOL; # 打印执行顺序getUserId
       return $this->user_id;
    }
```

发现上诉无论如何都拿不到user_id

研究了一下 laravel请求周期

#### 执行结果

```php
__construct
middleware
getUserId
```



### 请求周期

```php
new Controller()
↓
__construct()
↓
middleware pipeline
↓
controller method
```

### thinkphp执行可以先中间件再__construct