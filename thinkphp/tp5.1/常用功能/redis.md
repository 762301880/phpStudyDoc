# tp5配置redis





# 添加统一redis单例模式统一入口

**封装统一单例**

```php
<?php


namespace app\common\service;


use think\cache\driver\Redis;

class RedisService
{
    private static $instance;

    public static function getInstance()
    {
        if (empty(self::$instance)) {
            self::$instance = new Redis([
                'host' => '3.81.234.214',
                'port' => 11981,
                'password' => 'yly274325132'
            ]);
        }
        return self::$instance;
    }
}
```

**调用**

```php
public function index()
    {
        $redis=RedisService::getInstance();
        $redis->set('name',132);
        return $redis->get('name');
    }
```

