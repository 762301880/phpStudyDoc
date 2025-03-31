# 说明&资料

## 说明

> `LaravelS`是胶水，用于快速集成`Swoole`到`Laravel`或`Lumen`，然后赋予它们更好的性能、更多可能性
>
> 总体来说laravels是一个集成到laravel中的swoole框架

## 资料

| name                                              | url                                                          |
| ------------------------------------------------- | ------------------------------------------------------------ |
| gitee-laravels文档   github中文文档  个人备份文档 | [link](https://gitee.com/zml956/laravel-s)  [link](https://github.com/hhxsv5/laravel-s/blob/PHP-8.x/README-CN.md)  [link](https://gitee.com/yao_liuyang/pdf_doc_backup/blob/master/laravels.md) |
| laravel学院-swoole从入门到实战                    | [link](https://laravelacademy.org/post/9801.html)            |
| 第三方博客                                        | [link](https://learnku.com/articles/35992)                   |
| packagist v3.4.4 php8.0以下可以用                 | [link](https://packagist.org/packages/hhxsv5/laravel-s#v3.7.40) |

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
# 配置文件：config/laravels.php
# 二进制文件：bin/laravels bin/fswatch bin/inotify

# 注意  请将config/laravels.php  里面的 listen_ip 127.0.0.1 改为0.0.0.0 
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

### 控制器中发送消息给websocket用户

> 注意踩坑 不可以调用nginx 转发给swoole端口的地址  请直接调用swoole存在的打开的 php端口

```shell
    public function sendMessage(Request $request)
    {
        $fd = $request->input("fd");
        $message = $request->input("message");
        if (!$fd || !$message) return response()->json(["error" => "参数fd 或者 message为空"]);
        $swoole = app('swoole');
        if (!$swoole->stats()) return response()->json(["error" => "websocket实例不存在"]);
        if (!$swoole->exist($fd)) return response()->json(["error" => "fd:{$fd},不是有效的websocket链接"]);
        $swoole->push($fd, $message);
        return response()->json(["success" => "消息已发送"]);
     }

# 调用发送
113.45.29.83:5200/api/websocket/send_message
```

**如果想让nginx转发可以在控制器中请求websocket**

```nginx
gzip on;
gzip_min_length 1024;
gzip_comp_level 2;
gzip_types text/plain text/css text/javascript application/json application/javascript application/x-javascript application/xml application/x-httpd-php image/jpeg image/gif image/png font/ttf font/otf image/svg+xml;
gzip_vary on;
gzip_disable "msie6";
map $http_upgrade $connection_upgrade {
    default upgrade;
}
upstream laravels {
    # By IP:Port
    server app:5200 weight=5 max_fails=3 fail_timeout=30s;
    # By UnixSocket Stream file
    #server unix:/xxxpath/laravel-s-test/storage/laravels.sock weight=5 max_fails=3 fail_timeout=30s;
    #server 192.168.1.1:5200 weight=3 max_fails=3 fail_timeout=30s;
    #server 192.168.1.2:5200 backup;
}
upstream swoole {
    # 通过 IP:Port 连接
    server app:5200 weight=5 max_fails=3 fail_timeout=30s;
    # 通过 UnixSocket Stream 连接，小诀窍：将socket文件放在/dev/shm目录下，可获得更好的性能
    #server unix:/yourpath/laravel-s-test/storage/laravels.sock weight=5 max_fails=3 fail_timeout=30s;
    #server 192.168.1.1:5200 weight=3 max_fails=3 fail_timeout=30s;
    #server 192.168.1.2:5200 backup;
    keepalive 16;
}
server {
    listen 9090;
    # 别忘了绑Host
    server_name 127.0.0.1;
    root /data/work/laravel_study/public;
    access_log /data/work/laravel_study/storage/logs/nginx/$server_name.access.log  main;
    autoindex off;
    index index.html index.htm;
    # Nginx处理静态资源(建议开启gzip)，LaravelS处理动态资源。
    location / {
        try_files $uri @laravels;
    }
    # 当请求PHP文件时直接响应404，防止暴露public/*.php
    #location ~* \.php$ {
    #    return 404;
    #}
    location =/ws {
            # proxy_connect_timeout 60s;
            # proxy_send_timeout 60s;
            # proxy_read_timeout：如果60秒内被代理的服务器没有响应数据给Nginx，那么Nginx会关闭当前连接；同时，Swoole的心跳设置也会影响连接的关闭
            # proxy_read_timeout 60s;
            proxy_http_version 1.1;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Real-PORT $remote_port;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header Host $http_host;
            proxy_set_header Scheme $scheme;
            proxy_set_header Server-Protocol $server_protocol;
            proxy_set_header Server-Name $server_name;
            proxy_set_header Server-Addr $server_addr;
            proxy_set_header Server-Port $server_port;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection $connection_upgrade;
            proxy_pass http://swoole;
        }
    location @laravels {
        # proxy_connect_timeout 60s;
        # proxy_send_timeout 60s;
        # proxy_read_timeout 120s;
        proxy_http_version 1.1;
        # 添加websocket配置让 LaravelS支持websocket
        proxy_set_header Connection "upgrade";         # 升级为websocket
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Real-PORT $remote_port;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_set_header Scheme $scheme;
        proxy_set_header Server-Protocol $server_protocol;
        proxy_set_header Server-Name $server_name;
        proxy_set_header Server-Addr $server_addr;
        proxy_set_header Server-Port $server_port;
        # “laravels”是指upstream
        proxy_pass http://laravels;
    }
}

```

