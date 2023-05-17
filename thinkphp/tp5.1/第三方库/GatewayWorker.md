## GatewayWorker资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| GatewayWorker官方文档 | [link](https://www.workerman.net/doc/gateway-worker/README.html) |
| 博客pdf               | [pdf](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/ThinkPHP%205.1%20Workerman%20%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20ThinkPHP5.1%20Workerman%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20%E7%9C%8B%E4%BA%91.pdf) |

## [tp下安装GatewayWorker](https://www.kancloud.cn/manual/thinkphp5_1/354134)

### 1. 直接安装封装好的扩展库

**安装服务端**

> composer安装

```php
composer require topthink/think-worker=2.0.*
```

**对应的配置文件解析**

> 这三个配置文件都属于 GatewayWorker 扩展包，用于实现 WebSocket 和 TCP 长连接服务的配置。
>
> - `gateway_worker.php` 是 GatewayWorker 服务的核心配置文件，它定义了服务的各种参数，如监听的 IP 和端口、进程数、Worker 和 Gateway 的运行模式、协议类型等。
> - `worker.php` 是 Worker 进程的配置文件，用于设置 Worker 进程相关的参数，例如进程的名称、任务处理逻辑、定时任务、各种事件的处理等。
> - `worker_server.php` 是 WorkerServer 进程的配置文件，通常用于设置服务器的一些基本参数，例如监听的 IP 和端口、WorkerServer 的运行模式等。

这三个配置文件中定义了不同层次的配置参数，通过它们，我们可以很方便地对 GatewayWorker 的各项功能和性能进行配置，并启动和管理相关的进程。





## bug解析

### 多进程情况下定时任务或者crontab定时任务执行多次的问题

> 问题:假如我在onWorkerStart中执行定时任务或者workerman/crontab 但是开启多个进程之后里面的任务会执行多次
>
> 在workerman中，onWorkerStart方法是在子进程启动时触发的，当启动多个子进程后，每个子进程都会执行一遍onWorkerStart方法，导致定时任务可能被重复执行。
>
> 解决这个问题的方法是，使用workerman/crontab组件的单例模式，确保所有的子进程共享一个计划任务列表。具体做法如下：

第一步，在onWorkerStart方法中初始化计划任务列表，并将其保存到一个全局变量中：

```php
use Workerman\Worker;
use Workerman\Crontab;

$worker = new Worker();
$worker->count = 4;
$worker->onWorkerStart = function($worker)
{
    global $crontab;
    // 初始化crontab实例
    $crontab = new Crontab();
    // 添加计划任务
    $crontab->addTask('* * * * *', function(){
        // 需要执行的任务
    });
};
Worker::runAll();
```



第二步，在定时任务的回调函数中添加进程编号的判断，只在主进程执行该任务，避免重复执行：

```php
use Workerman\Worker;
use Workerman\Crontab;

$worker = new Worker();
$worker->count = 4;
$worker->onWorkerStart = function($worker)
{
    global $crontab;
    // 初始化crontab实例
    $crontab = new Crontab();
    // 添加计划任务
    $crontab->addTask('* * * * *', function() use ($worker){
        // 判断是否在主进程中执行
        if($worker->id === 0) {
            // 需要执行的任务
        }
    });
};
Worker::runAll();
```

> 我是在 topthink/think-worker 扩展包 的worker_server.php中的onWorkerStart回调中写的怎么处理呢

```php
'onWorkerStart' => function ($worker) {
        if ($worker->id === 0) {
            \Workerman\Lib\Timer::add(10, function () {
                \think\facade\Log::info('555555555555555' . date('Y-m-d H:i:s'));
            });
            new \Workerman\Crontab\Crontab('1 * * * * *', function () {
                \think\facade\Log::info("aaaaaaaaaaaaaaaaaaa" . date('Y-m-d H:i:s'));
            });
        }
    },
```















