## redis单例模式

> 1. 创建一个 Redis 单例类，我们可以将它定义在 `app/Common/Service` 目录下，比如 `RedisService.php`

```php
<?php


namespace App\Services;


use Illuminate\Support\Facades\Redis;

class RedisService
{
    private static $instance = null;

    private function __construct()
    {
        // 私有化构造函数以避免类被实例化，只能通过 getInstance 方法获得类的唯一实例。
    }

    public static function getInstance()
    {
        if (is_null(self::$instance)) {
            self::$instance = Redis::connection()->client();
        }
        return self::$instance;
    }

    //因为__call只有在new实例下  未找到对应方法才会触发 所以单例模式下这个无效-废弃
//    public function __call($method, $arguments)
//    {
//        return call_user_func_array([self::getInstance(), $method], $arguments);
//    }
}
```

> 在这个 Laravel 的 Redis 单例实现中，`__call`方法是用于转发 Redis 实例的调用的。具体来说，当我们通过单例的 `getInstance` 方法获取到 Redis 实例时，如果我们想要使用某个 Redis 方法，可以直接通过该实例调用，比如：

```
$redis = RedisSingleton::getInstance();
$redis->set('foo', 'bar');
echo $redis->get('foo'); // 输出 'bar'
```

> 但是，如果我们想要调用 Redis 连接对象上不存在的方法，这时候如果直接使用 `$redis->nonexistentMethod()` 会触发“调用未定义的方法”错误。因此在 Redis 单例类中我们定义了 `__call` 方法，它将任何需要调用 Redis 方法的请求委托给单例的 Redis 连接处理，保证在应用中调用 Redis 的任何方法都经过单例处理，避免出现异常或错误。

> 在 `config/database.php` 文件中配置 Redis 连接参数

```php
'redis' => [
    // ...
    'default' => [
        'url' => env('REDIS_URL'),
        'host' => env('REDIS_HOST', '127.0.0.1'),
        'password' => env('REDIS_PASSWORD', null),
        'port' => env('REDIS_PORT', '6379'),
        'database' => env('REDIS_DB', '0'),
    ],
    // ...
],
```

> 在 Laravel 项目中使用 Redis 时，只需通过以下方式获取 Redis 实例

```php
$redis = RedisSingleton::getInstance();
$redis->set('foo', 'bar');
echo $redis->get('foo'); // 输出 'bar'
```

