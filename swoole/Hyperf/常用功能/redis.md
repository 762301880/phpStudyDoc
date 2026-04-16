 

## 资料

| 名称         | 地址                                              |
| ------------ | ------------------------------------------------- |
| 官方文档参考 | [link](https://www.hyperf.wiki/2.2/#/zh-cn/redis) |

**Hyperf** 里用 Redis 框架已经帮你把连接池、协程这些都封装好了。只需要配好配置 + 调用就行。

## 安装 Redis 组件

```php
composer require hyperf/redis
```

## 配置 Redis

编辑 `config/autoload/redis.php`（没有就创建）：

```php
<?php

return [
    'default' => [
        'host' => '127.0.0.1',
        'auth' => null,
        'port' => 6379,
        'db' => 0,
        'pool' => [
            'min_connections' => 1,
            'max_connections' => 50,
            'connect_timeout' => 10.0,
            'wait_timeout' => 3.0,
            'heartbeat' => -1,
            'max_idle_time' => 60.0,
        ],
    ],
];
```

**注释配置**

```php
<?php

return [
    'default' => [
        // Redis服务器地址
        'host' => '127.0.0.1',

        // 密码，没有就 null
        'auth' => null,

        // 端口，默认 6379
        'port' => 6379,

        // 使用哪个数据库（0-15）
        'db' => 0,

        // ⭐ 重点：连接池配置（协程核心）
        'pool' => [
            // 最小连接数（启动时就会创建）
            'min_connections' => 1,

            // 最大连接数（并发上限，防止打爆 Redis）
            'max_connections' => 50,

            // 连接超时时间（秒）
            // 👉 建立 TCP 连接最多等多久
            'connect_timeout' => 10.0,

            // 等待连接池连接的超时时间（秒）
            // 👉 如果连接池用光了，最多等多久拿连接
            'wait_timeout' => 3.0,

            // 心跳检测间隔（秒）
            // -1 表示不做心跳检测
            // 👉 用来检测连接是否还活着
            'heartbeat' => -1,

            // 连接最大空闲时间（秒）
            // 👉 超过这个时间没用的连接会被回收
            'max_idle_time' => 60.0,
        ],
    ],
];
```



这里的重点是 **连接池**，Hyperf 是协程模型，不能用传统单连接

## 最常用用法（推荐）

### 方式1：依赖注入（推荐）

```php
   use Hyperf\Redis\Redis;


    protected $redis = null;

    public function __construct(Redis $redis)
    {
        $this->redis = $redis;
    }

    public function test()
    {
        $this->redis->set('name', 'liuyang');
        return $this->redis->get('name');
    }
```

 这是最优雅、最推荐的写法（自动走连接池)

### 方式2：通过容器获取

```php
$redis = container()->get(Hyperf\Redis\Redis::class);

$redis->set('key', 'value');
echo $redis->get('key');
```

适合在工具类 / 非注入场景

## 在协程里的注意点（重点）

❗ Hyperf 是协程框架，注意：

- 不要自己 new Redis（会炸连接）
- 一定用容器 / 注入
- 不要长时间持有 Redis 实例（用完就好）

## 封装统一单例模式

```php
<?php


namespace App\Service;

use Hyperf\Redis\Redis;
use Hyperf\Utils\ApplicationContext;

class RedisService
{

    public static function getInstance(): Redis
    {
        $container = ApplicationContext::getContainer();
        return $container->get(Redis::class);
    }
}
```

**补充**

为什么不（千万别这样做）

```php
private static $redis;

public static function getInstance()
{
    if (!self::$redis) {
        self::$redis = container()->get(Redis::class);
    }
    return self::$redis;
}
```

👉 这个就危险了：

- Redis 对象被多个协程复用
- 连接不会正确释放回连接池
- 高并发可能异常