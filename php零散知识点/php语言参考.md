

##  [魔术方法](https://www.php.net/manual/zh/language.oop5.magic.php)

### __call

> 在对象中调用一个不可访问方法时，[__call()](https://www.php.net/manual/zh/language.oop5.overloading.php#object.call) 会被调用。

**错误案例**

> 以前写代码就是**囫囵吞枣**  ai教我的拿过来用 就行了 看到下面的 **__call**  好奇为什么一只没有进入过方法
>
> 原来一查 **__call**方法是给 对象调用的 我这里静态单例模式 无论如何走不到**__call**方法中的  所以说没事还要多看文档

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
            self::$instance = Redis::connection();
        }
        return self::$instance;
    }

    # 错误使用
    public function __call($method, $arguments)
    {
        return call_user_func_array([self::getInstance(), $method], $arguments);
    }
}



```

