# 	说明

> 使用swoole的时候经常会看见端口被占用的情况如下所示

```shell
bash-5.0# php bin/hyperf.php start

In Server.php line 170:
failed to listen server port[0.0.0.0:9802], Error: Address in use[98]
```

## 解决方法

**查看被占用的端口**

> netstat  -a或--all 显示所有连线中的Socket 
>
> -n或--numeric 直接使用IP地址，而不通过域名服务器。
>
> -p或--programs 显示正在使用Socket的程序识别码和程序名称。

```shell
bash-5.0# netstat  -anp  |  grep 9802
tcp        0      0 0.0.0.0:9802            0.0.0.0:*               LISTEN      16/skeleton.Master
```

**杀死进程**

> 使用***kill***杀死进程

```shell
bash-5.0# kill 16
bash-5.0# php bin/hyperf.php start
[INFO] Worker#0 started.
[INFO] HTTP Server listening at 0.0.0.0:9802
[INFO] Worker#1 started.
[INFO] Process[crontab-dispatcher.0] start.
[INFO] Process[queue.default.0] start.
string(31) "sourceCount_2021-11-09 11:29:01"
```

##  公网ip请求 无法访问容器内部9502端口

**问题排查**

>  $server = new Server('127.0.0.1', 9502, false);
>
> 本来是挺简单的问题  说明还是因为不够心细啊   发现公网ip:9502 端口就是无法请求成功容器内开启的  9502端口 排查了好多问题 重启也没用
>
> 突然发现 是因为   ip没设置好 设置为**0.0.0.0**就是可以任意ip请求

**解决问题**

```php
<?php
use Swoole\Coroutine\Http\Server;
use function Swoole\Coroutine\run;

//测试 专用
/**
 * Server('0.0.0.0', 9502, false)
 * 最好设置为0.0.0.0  不然的话 就算你开了容器9502端口也无法访问容器内启动的9502
 */
run(function () {
    $server = new Server('0.0.0.0', 9502, false);
    $server->handle('/', function ($request, $response) {
        $response->end("<h1>Index</h1>");
    });
    $server->handle('/test', function ($request, $response) {
        $response->end("<h1>Test</h1>");
    });
    $server->handle('/stop', function ($request, $response) use ($server) {
        $response->end("<h1>Stop</h1>");
        $server->shutdown();
    });
    $server->start();
});
echo 1;//得不到执行

```

