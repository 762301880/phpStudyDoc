# tp5配置redis

## [复合类型使用redis配置](https://www.kancloud.cn/manual/thinkphp5_1/354116)

```php
 $redis = Cache::store('default')->handler();
 $redis->set('test',12345);
```

**config\cache配置**

```php
return [
    // 使用复合缓存类型
    'type'  =>  'complex',
    // 默认使用的缓存
    'default'   =>  [
        // 驱动方式
        'type'   => 'file',
        // 缓存保存目录
        'path'   => '../runtime/default',
    ],
    // 文件缓存
    'file'   =>  [
        // 驱动方式
        'type'   => 'file',
        // 设置不同的缓存保存目录
        'path'   => '../runtime/file/',
    ],  
    // redis缓存 -建议修改为兼容env配置
    'redis'   =>  [
        // 驱动方式
        'type'   => 'redis',
        // 服务器地址
        'host'       => '127.0.0.1',
        'port'=>'6379'
        'password'=>''
    ],     
],
```

## 实例化redis配置



```shell
$redis=new \think\cache\driver\Redis();
```

**配置选项**

> 具体配置可以查阅底层`\thinkphp\library\think\cache\driver\Redis.php`
>
> **<font color='red'>建议封装为单例模式统一调用</font>**

```shell
$redis=new \think\cache\driver\Redis(
[
        'host'       => '127.0.0.1',
        'port'       => 6379,
        'password'   => '',
        'select'     => 0,
        'timeout'    => 0,
        'expire'     => 0,
        'persistent' => false,
        'prefix'     => '',
        'serialize'  => true,
]
);
```



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

