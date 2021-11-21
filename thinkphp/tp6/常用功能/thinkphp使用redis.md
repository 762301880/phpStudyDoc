#  配置



## 资料

| thinkphp6.0-缓存 | [link](https://www.kancloud.cn/manual/thinkphp6_0/1037634) |
| ---------------- | ---------------------------------------------------------- |
|                  |                                                            |
|                  |                                                            |



## 配置redis

> 注意：由于我们采用的是原生redis写法所以php 需要开启**redis**扩展配置,请终端使用**php -m** 查看是否开启
>
> 没有开启请网上自行查找解决方案
>
> 找到**config\cache**中配置添加**redis**

```shell
<?php

// +----------------------------------------------------------------------
// | 缓存设置
// +----------------------------------------------------------------------

return [
    // 默认缓存驱动
    'default' => env('cache.driver', 'file'),

    // 缓存连接方式配置
    'stores'  => [
        'file' => [
            // 驱动方式
            'type'       => 'File',
            // 缓存保存目录
            'path'       => '',
            // 缓存前缀
            'prefix'     => '',
            // 缓存有效期 0表示永久缓存
            'expire'     => 0,
            // 缓存标签前缀
            'tag_prefix' => 'tag:',
            // 序列化机制 例如 ['serialize', 'unserialize']
            'serialize'  => [],
        ],
        // 更多的缓存连接
        // redis缓存
    'redis'   =>  [
        // 驱动方式
        'type'   => 'redis',
        // 服务器地址
        'host'    => '127.0.0.1',
    ],   
    ],
];

```





## 代码实例

```shell
        use think\cache\driver\Redis;   
        
        $redis = new Redis();
        $redis->set('name','zhangsan'); 
        dd($redis->get('name'));
```

#  bug

## serialize error at offset...

> 有这样一个问题我需要从redis中读取别的端存储的的token发现,报错**serialize error at offset**
>
> 查看报错位置**\vendor\topthink\framework\src\think\Driver.php**如下所示
>
> 这个原因是thinkphp使用redis保存的时候会自动给你序列化,读取的时候也会自动给你
>
> 反序列化, 而laravel保存的是原数据，所以需要到laravel端使用redis的时候手动序列化和反序列化

```php
    /**
     * 序列化数据
     * @access protected
     * @param mixed $data 缓存数据
     * @return string
     */
    protected function serialize($data): string
    {
        if (is_numeric($data)) {
            return (string) $data;
        }

        $serialize = $this->options['serialize'][0] ?? "serialize";

        return $serialize($data);
    }
```

> 可以看出如果是数值类型直接返回元数据,反之返回序列化数据

**解决方案**

```shell
# 在别的端加密token的时候手动序列化一下value
$redis->set('token',serialize('token'));

# 别的端解密的时候手动的解密一下结果
$token=unserialize($redis->get('token'));
```

