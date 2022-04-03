# 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link ](https://blog.csdn.net/will5451/article/details/80434174)  [link](https://www.jianshu.com/p/9ceebfb6ade0) |
| github官方文档 | [link](https://github.com/top-think/think-queue/tree/2.0)    |

#  安装&使用

**安装**

> 使用composer 命令安装

```php
composer require topthink/think-queue=2.*
```

**配置文件**

>配置文件位于 `config/queue.php`

### 公共配置

```php
[
    'connector'=>'sync' //驱动类型，可选择 sync(默认):同步执行，database:数据库驱动,redis:Redis驱动,topthink:Topthink驱动
                   //或其他自定义的完整的类名
]
# 对应配置如下
return [
       'connector'  => 'Redis',         // Redis 驱动
       'expire'     => 60,              // 任务的过期时间，默认为60秒; 若要禁用，则设置为 null 
       'default'    => 'default',       // 默认的队列名称
       'host'       => '127.0.0.1',     // redis 主机ip
       'port'       => 6379,            // redis 端口
       'password'   => '',              // redis 密码
       'select'     => 1,               // 使用哪一个 db，默认为 db0
       'timeout'    => 0,               // redis连接的超时时间
       'persistent' => false,           // 是否是长连接
     
   //    'connector' => 'Database',   // 数据库驱动
   //    'expire'    => 60,           // 任务的过期时间，默认为60秒; 若要禁用，则设置为 null
   //    'default'   => 'default',    // 默认的队列名称
   //    'table'     => 'jobs',       // 存储消息的表名，不带前缀
   //    'dsn'       => [],

   //    'connector'   => 'Topthink',   // ThinkPHP内部的队列通知服务平台 ，本文不作介绍
   //    'token'       => '',
   //    'project_id'  => '',
   //    'protocol'    => 'https',
   //    'host'        => 'qns.topthink.com',
   //    'port'        => 443,
   //    'api_version' => 1,
   //    'max_retries' => 3,
   //    'default'     => 'default',

   //    'connector'   => 'Sync',       // Sync 驱动，该驱动的实际作用是取消消息队列，还原为同步执行
   ];
```

### 驱动配置

> 各个驱动的具体可用配置项在`think\queue\connector`目录下各个驱动类里的`options`属性中，写在上面的`queue`配置里即可覆盖

