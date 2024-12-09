## [菜鸟教程-设计模式之单例模式](https://www.runoob.com/design-pattern/singleton-pattern.html)

## 什么是单例模式

> 单例模式是一种创建型模式，在一个系统中只有一个实例对象，且该实例可以被系统中的任何代码所访问和修改。单例模式的目的在于保证系统中一个类只有一个实例，并提供一个访问它的全局访问点。
>
> 单例模式适用于一些被重复使用的对象，例如全局日志对象、全局配置对象、数据库连接等。从实现角度来说，许多现代编程语言都提供了线程安全、饿汉式、懒汉式等多种单例模式的实现方式。
>
> 单例模式的优点在于：
>
> - 提供了对唯一实例的受控访问，避免了多个实例竞争资源造成的冲突问题，确保了全局状态的一致性。
> - 由于单例对象只有一个实例，因此可以节省系统资源、降低系统复杂度、提高系统的可维护性。
> - 单例模式可以允许客户端（调用者）自由访问唯一实例，方便系统的扩展和修改。
>
> 在一些情况下，滥用单例模式可能会带来一些问题，比如：
>
> - 单例模式可能被滥用为全局变量，导致系统中的代码紧密耦合、难以维护、难以测试。
> - 单例模式对扩展开放，对修改封闭，对于一些频繁变化的场景（如并发操作、多线程处理等），使用单例模式会带来一些不必要的限制。
> - 单例模式在实现上可能会产生一些竞争和线程同步问题，需要仔细处理。
>
> 因此，在使用单例模式时需要注意平衡其优点与缺点，避免滥用。

## laravel实现单例模式

> 1. 创建一个 Redis 单例类，我们可以将它定义在 `app/Helpers` 目录下，比如 `RedisSingleton.php`

```php
<?php

namespace App\Helpers;

use Illuminate\Support\Facades\Redis;

class RedisSingleton
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

    public function __call($method, $arguments)
    {
        return call_user_func_array([self::getInstance(), $method], $arguments);
    }
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

