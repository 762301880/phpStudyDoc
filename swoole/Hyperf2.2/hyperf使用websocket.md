# 资料

| name           | url                                                          |
| -------------- | ------------------------------------------------------------ |
| WebSocket 服务 | [WebSocket 服务](https://www.hyperf.wiki/2.1/#/zh-cn/websocket-server) |
|                |                                                              |

# 安装&使用

## 安装

### 在容器中使用composer命令安装

```shell
composer require hyperf/websocket-server
```

### 配置 Server

> 修改 `config/autoload/server.php`，增加以下配置。

```shell
<?php

return [
    'servers' => [
       [
            'name' => 'http',
            'type' => Server::SERVER_HTTP,
            'host' => '0.0.0.0',
            'port' => 9501,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_REQUEST => [Hyperf\HttpServer\Server::class, 'onRequest'],
            ],
        ],      #原有的http服务不要去修改它的
        # 在此位置添加websocket服务
        [
            'name' => 'ws',
            'type' => Server::SERVER_WEBSOCKET,
            'host' => '0.0.0.0',
            'port' => 9502,
            'sock_type' => SWOOLE_SOCK_TCP,
            'callbacks' => [
                Event::ON_HAND_SHAKE => [Hyperf\WebSocketServer\Server::class, 'onHandShake'],
                Event::ON_MESSAGE => [Hyperf\WebSocketServer\Server::class, 'onMessage'],
                Event::ON_CLOSE => [Hyperf\WebSocketServer\Server::class, 'onClose'],
            ],
        ],
    ],
];
```

### 配置路由

> 在 `config/routes.php` 文件内增加对应 `ws` 的 Server 的路由配置，
>
> 这里的 `ws` 值取决于您在 `config/autoload/server.php` 内配置的 WebSocket Server 的 `name` 值。

```php
<?php

Router::addServer('ws', function () {
    Router::get('/', 'App\Controller\WebSocketController');
});
```

#### 补充

这个看起来像 HTTP GET 路由，但其实在 WS 里，它的意义是：

👉 **定义 WebSocket 握手 (handshake) 的入口**

也就是：

当客户端连接：

```
ws://你的域名:端口/
```

👉 就会走这个 `/` 路由
 👉 然后进入 `WebSocketController`

 实际触发流程

浏览器或客户端发起连接：

```
new WebSocket("ws://127.0.0.1:9502/");
```

流程是：

1. 发 HTTP Upgrade 请求（握手）
2. Hyperf 用 WS 路由匹配 `/`
3. 命中这个 Controller
4. 然后进入你在 Controller 里的方法，比如：

```php
public function onOpen($server, $request)
```

那这个 Controller 干啥用？

在 Hyperf WebSocket 里，这个 Controller 本质上是：

👉 WebSocket 生命周期处理器

通常你会实现：

```php
public function onOpen($server, $request)
public function onMessage($server, $frame)
public function onClose($server, $fd)
```

为什么看起来像 HTTP？

因为：

👉 WebSocket 握手本质就是 HTTP 请求升级（Upgrade）

所以 Hyperf 复用了 HTTP Router 来匹配路径

一句话总结

这段代码的作用就是：

👉 **指定 WebSocket 连接 `/` 路径时，交给 `WebSocketController` 处理**







### 创建对应控制器

```shell
<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\Contract\OnCloseInterface;
use Hyperf\Contract\OnMessageInterface;
use Hyperf\Contract\OnOpenInterface;
use Swoole\Http\Request;
use Swoole\Server;
use Swoole\Websocket\Frame;
use Swoole\WebSocket\Server as WebSocketServer;

class WebSocketController implements OnMessageInterface, OnOpenInterface, OnCloseInterface
{
    # 发送消息时触发
    public function onMessage($server, Frame $frame): void
    {
        $server->push($frame->fd, 'Recv: ' . $frame->data);
    }
    # 关闭websocket时触发
    public function onClose($server, int $fd, int $reactorId): void
    {
        var_dump('closed');
    }
    # 连接websocket时触发
    public function onOpen($server, Request $request): void
    {
        $server->push($request->fd, 'Opened');
    }
}
```

### 启动服务

接下来启动 Server，便能看到对应启动了一个 WebSocket Server 并监听于 9502 端口，此时您便可以通过各种 WebSocket Client 来进行连接和数据传输了。

```php
$ php bin/hyperf.php start

[INFO] Worker#0 started.
[INFO] WebSocket Server listening at 0.0.0.0:9502
[INFO] HTTP Server listening at 0.0.0.0:9501    # 我们选择连接9501的端口可以在在线websocket服务中测试
```

