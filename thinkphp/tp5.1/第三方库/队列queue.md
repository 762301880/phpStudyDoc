# 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link ](https://blog.csdn.net/will5451/article/details/80434174)  [link](https://www.jianshu.com/p/9ceebfb6ade0) |
| github官方文档 | [link](https://github.com/top-think/think-queue/tree/2.0)    |

#  安装&使用

## **安装**

> 使用composer 命令安装

```php
composer require topthink/think-queue=2.*
```

**配置文件**

>配置文件位于 `config/queue.php`
>
>**驱动配置**
>
>各个驱动的具体可用配置项在`think\queue\connector`目录下各个驱动类里的`options`属性中，写在上面的`queue`配置里即可覆盖
>
>项目/vendor/topthink/think-queue/src/queue/connector/Redis.php

公共配置

> 推荐使用**redis**配置

```php
# 原本自带默认配置
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



## 使用

**创建任务类**

> 单模块项目推荐使用 `app\job` 作为任务类的命名空间 多模块项目可用使用 `app\module\job` 作为任务类的命名空间 
>
> 也可以放在任意可以自动加载到的地方
>
> 任务类不需继承任何类，如果这个类只有一个任务，那么就只需要提供一个`fire`方法就可以了，如果有多个小任务，就写多个方法，下面发布任务的时候会有区别
> 每个方法会传入两个参数 `think\queue\Job $job`（当前的任务对象） 和 `$data`（发布任务时自定义的数据）
>
> 还有个可选的任务失败执行的方法 `failed` 传入的参数为`$data`（发布任务时自定义的数据）

### 下面写两个例子

```php
namespace app\job;

use think\queue\Job;

class Job1{
    
    public function fire(Job $job, $data){
    
            //....这里执行具体的任务 
            
             if ($job->attempts() > 3) {
                  //通过这个方法可以检查这个任务已经重试了几次了
             }
            
            
            //如果任务执行成功后 记得删除任务，不然这个任务会重复执行，直到达到最大重试次数后失败后，执行failed方法
            $job->delete();
            
            // 也可以重新发布这个任务
            $job->release($delay); //$delay为延迟时间
          
    }
    
    public function failed($data){
    
        // ...任务达到最大重试次数后，失败了
    }

}
namespace app\lib\job;

use think\queue\Job;

class Job2{
    
    public function task1(Job $job, $data){
    
          
    }
    
    public function task2(Job $job, $data){
    
          
    }
    
    public function failed($data){
        
    }

}
```

## 发布任务

> `think\Queue::push($job, $data = '', $queue = null)` 和 `think\Queue::later($delay, $job, $data = '', $queue = null)` 两个方法，前者是立即执行，后者是在`$delay`秒后执行

```php
$job` 是任务名
单模块的，且命名空间是`app\job`的，比如上面的例子一,写`Job1`类名即可
多模块的，且命名空间是`app\module\job`的，写`model/Job1`即可
其他的需要些完整的类名，比如上面的例子二，需要写完整的类名`app\lib\job\Job2`
如果一个任务类里有多个小任务的话，如上面的例子二，需要用@+方法名`app\lib\job\Job2@task1`、`app\lib\job\Job2@task2
```

`$data` 是你要传到任务里的参数

`$queue` 队列名，指定这个任务是在哪个队列上执行，同下面监控队列的时候指定的队列名,可不填

## 监听任务并执行

> php think queue:listen

> php think queue:work --daemon（不加--daemon为执行单个任务）

两种，具体的可选参数可以输入命令加 --help 查看

> 可配合supervisor使用，保证进程常驻