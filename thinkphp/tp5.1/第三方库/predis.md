# 说明

> tp5自带的**reids**扩展类支持的命令很少所以我们安装第三方的**redis**扩展包使用，支持的命令也很广泛

## 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://blog.csdn.net/qq_41082746/article/details/102825951) |

## 安装&使用

**安装**

```php
composer require predis/predis
```

**使用**

```php
<?php

namespace app\admin\controller;
use Predis\Client;
class Demo
{
    use ApplyResponseLayout;

    private $_redis;

    public function __construct()
    {
        $this->_redis = new Client([
            'host' => 'redis-13822.c258.us-east-1-4.ec2.cloud.redislabs.com',
            'port' => 13822,
            'connectTimeout' => 2.5,
            'auth' => ['phpredis', 'phpredis'],
            'password' => '自己的redis密码',
            'ssl' => ['verify_peer' => false],
        ]);
    }
    public function test()
    {
         dd($this->_redis->set(456,456,'NX','EX',10)); //上锁成功修改返回结果);
    }
}
```

