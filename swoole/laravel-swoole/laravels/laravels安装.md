# 说明&资料

## 说明

> `LaravelS`是胶水，用于快速集成`Swoole`到`Laravel`或`Lumen`，然后赋予它们更好的性能、更多可能性
>
> 总体来说laravels是一个集成到laravel中的swoole框架

## 资料

| name                                | url                                                          |
| ----------------------------------- | ------------------------------------------------------------ |
| gitee-laravels文档   github中文文档 | [link](https://gitee.com/zml956/laravel-s)  [link](https://github.com/hhxsv5/laravel-s/blob/PHP-8.x/README-CN.md) |
| laravel学院-swoole从入门到实战      | [link](https://laravelacademy.org/post/9801.html)            |
| 第三方博客                          | [link](https://learnku.com/articles/35992)                   |
| packagist v3.4.4 php8.0以下可以用   | [link](https://packagist.org/packages/hhxsv5/laravel-s#v3.7.40) |

# 安装&使用

## 安装

- 使用***composer***安装**laravelS**

```shell
# PHP >=8.2
composer require "hhxsv5/laravel-s:~3.8.0"

# PHP >=5.5.9,<=7.4.33
# composer require "hhxsv5/laravel-s:~3.7.0"

# 确保你的composer.lock文件是在版本控制中

# 我的
composer require "hhxsv5/laravel-s:~3.4.4"
```

- 安装完成之后发布配置文件

> 该扩展包具备自动发现功能（Laravel 5.5 以上版本可用），无需手动在 `config/app.php` 配置文件中注册，安装完成后，运行如下 Artisan 命令相应脚本和配置文件发布到根目录下：

```shell
php artisan laravels publish
# 该命令会发布配置文件 laravels.php 到 config 目录下，以及脚本文件到 bin 目录下：
yaoliuyang@benben:~/公共的/phpProject/laravel_study$ php artisan laravels publish
Copied file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/config/laravels.php] To [/home/yaoliuyang/公共的/phpProject/laravel_study/config/laravels.php]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/laravels] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/laravels]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/fswatch] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/fswatch]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/inotify] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/inotify]    
```

- ### 启动 LaravelS

```shell
php bin/laravels start
# 启动后的命令
yaoliuyang@benben:~/公共的/phpProject/laravel_study$ php bin/laravels start
 _                               _  _____ 
| |                             | |/ ____|
| |     __ _ _ __ __ ___   _____| | (___  
| |    / _` | '__/ _` \ \ / / _ \ |\___ \ 
| |___| (_| | | | (_| |\ V /  __/ |____) |
|______\__,_|_|  \__,_| \_/ \___|_|_____/ 
                                           
Speed up your Laravel/Lumen
>>> Components
+---------------------------+---------+
| Component                 | Version |
+---------------------------+---------+
| PHP                       | 7.4.3   |
| Swoole                    | 4.6.7   |
| LaravelS                  | 3.7.20  |
| Laravel Framework [local] | 8.53.1  |
+---------------------------+---------+
>>> Protocols
+-----------+--------+-------------------+-----------------------+
| Protocol  | Status | Handler           | Listen At             |
+-----------+--------+-------------------+-----------------------+
| Main HTTP | On     | Laravel Framework | http://127.0.0.1:5200 |
+-----------+--------+-------------------+-----------------------+
>>> Feedback: https://github.com/hhxsv5/laravel-s
[2021-08-15 12:37:07] [TRACE] Swoole is running, press Ctrl+C to quit.
```

- laravel 启动命令解释

![命令](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/35dcac73c81020ff6c06e346563fcc34.jpg)

```php
  /**
   * 这样，Swoole 服务就被启动起来，监听本地的 5200 端口，如果有请求发送到这个端口，它就可以进行处理。
   * 此外 php bin/laravels 还支持其它命令对 LaravelS 进行管理：
   */
# 监听ip 默认是127.0.0.1 推荐修改为0.0.0.0(r)
'listen_ip' => env('LARAVELS_LISTEN_IP', '0.0.0.0'),
# 端口可以在config\laravels.php中的进行修改
'listen_port' => env('LARAVELS_LISTEN_PORT', 5200),# 修改laravels监听启动端口    
```

- 生产环境请使用守护进程

> #### 通过 Supervisor 管理 LaravelS
>
> 如果是在生产环境使用，推荐使用 Supervisor 对 LaravelS 服务进行管理，从而提供服务的稳定性，相应的配置示例如下：

```shell
[program:laravel-s-test]
command=/user/local/bin/php /path/to/project/bin/laravels start -i
numprocs=1
autostart=true
autorestart=true
startretries=3
user=www-data
redirect_stderr=true
stdout_logfile=/path/to/project/storage/logs/supervisord-stdout.log
# 其中 /path/to/project 为 Web 项目的目录，你可以根据自己的项目路径进行修改。
```

## laravel-laravels使用swoole-websocket

> 要基于该扩展包实现 WebSocket 服务器，首先首先需要创建一个实现了 `Hhxsv5\LaravelS\Swoole\WebSocketHandlerInterface` 接口的 `WebSocketService` 类：

###  建立websocket服务

> 在app\services 中建立  WebSocketService.php 实现 WebSocketHandlerInterface接口

```php
<?php

namespace App\Services;

use Hhxsv5\LaravelS\Swoole\WebSocketHandlerInterface;
use Illuminate\Support\Facades\Log;
use Swoole\Http\Request;
use Swoole\WebSocket\Frame;
use Swoole\WebSocket\Server;

class WebSocketService implements WebSocketHandlerInterface
{

    public function __construct()
    {

    }

    // 连接建立时触发
    public function onOpen(Server $server, Request $request)
    {
        // 在触发 WebSocket 连接建立事件之前，Laravel 应用初始化的生命周期已经结束，你可以在这里获取 Laravel 请求和会话数据
        // 调用 push 方法向客户端推送数据，fd 是客户端连接标识字段
        Log::info('WebSocket 连接建立');
        $server->push($request->fd, 'Welcome to WebSocket Server built on LaravelS');
    }

    // 收到消息时触发
    public function onMessage(Server $server, Frame $frame)
    {
        // 调用 push 方法向客户端推送数据
        $server->push($frame->fd, 'This is a message sent from WebSocket Server at ' . date('Y-m-d H:i:s'));
    }

    // 关闭连接时触发
    public function onClose(Server $server, $fd, $reactorId)
    {
        Log::info('WebSocket 连接关闭');
    }
}

```

### 修改配置文件实现swoole

> 在config\laravels.php中启用 WebSocket 通信并将刚刚创建的服务器类配置到对应的配置项：

```php
'websocket' => [
    'enable' => true,
    'handler' => \App\Services\WebSocketService::class,
],
```

- 我们还可以在 `swoole` 配置项中配置 WebSocket 长连接的强制关闭逻辑：

```shell
'swoole' => [
    ...
    
    // 每隔 60s 检测一次所有连接，如果某个连接在 600s 内都没有发送任何数据，则关闭该连接
    'heartbeat_idle_time'      => 600,
    'heartbeat_check_interval' => 60,
    
    ...
],
```

