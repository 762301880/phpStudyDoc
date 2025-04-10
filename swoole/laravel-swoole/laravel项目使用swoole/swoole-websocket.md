# 资料

| 资料名称       | 资料地址                                                     |
| -------------- | ------------------------------------------------------------ |
| swoole入门指引 | [链接](https://wiki.swoole.com/wiki/page/1.html)             |
| swoole官方文档 | [链接](https://wiki.swoole.com/#/)  [旧版文档](https://wiki.swoole.com/wiki/page/987.html) |
| swoole在线测试 | [链接](http://coolaf.com/tool/chattest)                      |
| 第三方博客参考 | [链接](https://www.cnblogs.com/phpk/p/10930481.html) [链接](http://www.phpbloger.com/article/421.html) [链接](https://segmentfault.com/a/1190000019639051)  [链接](https://wenku.baidu.com/view/05535668e75c3b3567ec102de2bd960590c6d90e.html) [链接](https://learnku.com/articles/19448) |

# laravel中使用swoole

## 创建command

> [关于command命令可以查看官方文档](https://learnku.com/docs/laravel/8.x/artisan/9387#tinker)  

```shell
php artisan make:command Swoole     # 创建command类
```

- 创建的swoole

  ```php
  <?php
  
  namespace App\Console\Commands;
  
  use Illuminate\Console\Command;
  use Swoole\Http\Request;
  use Swoole\Http\Response;
  use Swoole\WebSocket\Frame;
  use Swoole\WebSocket\Server;
  
  class Swoole extends Command
  {
      /**
       * The name and signature of the console command.
       *
       * @var string
       */
      protected $signature = 'swoole {action?}';
  
      /**
       * 命令说明
       * The console command description.
       * @var string
       */
      protected $description = 'swoole test';
  
      /**
       * Create a new command instance.
       *
       * @return void
       */
      public function __construct()
      {
          parent::__construct();
      }
  
      /**
       * Execute the console command.
       *
       * @return int
       */
      public function handle()
      {
          $action = $this->argument('action');
          switch ($action) {
              case 'restart':
                  $this->info('swoole server restarted');
                  break;
              case 'close':
                  $this->info('swoole server stoped');
                  break;
              default:
                  $this->start();
                  break;
          }
      }
  
      public function start()
      {
          # 开启 websocket服务 参数一指定任意域名 参数二 端口
          $ws = new \swoole_websocket_server('0.0.0.0', 1997);
          $ws->set([
              # 设置心跳检测 时间一到会自动断开连接 https://wiki.swoole.com/#/server/setting?id=heartbeat_check_interval
              'heartbeat_idle_time' => 600, // 表示一个连接如果600秒内未向服务器发送任何数据，此连接将被强制关闭
              'heartbeat_check_interval' => 60,  // 表示每60秒遍历一次
          ]);
          //$this->ws->on('open', [$this, 'open']);  # 参数二可以采用闭包方式访问 数组中第一个参数表示 类对象 参数二表示 类方法
          # 用户连接事件
          /**
           * $server 这个server就是指上面创建的websocket服务器
           * $request 指的是客户端,谁连接到我了,传输过来的信息
           * $request->fd 指的是客户端的唯一编号
           */
          $ws->on('open', function (Server $server, Request $request) {
              $server->bind($request->fd,'401');# 绑定用户 第二个参数暂时写死
              echo "server: handshake success with fd{$request->fd}\n";
          });
          /**
           * 用户发送消息事件
           * 客户端向服务端发送消息时调用该事件
           * $frame 客户端发送消息的信息
           * $frame->fd 客户端的唯一编号
           * $frame->data 客户端发送消息的文本内容
           *
           * $server->push('客户端的唯一编号','消息内容(一般传递json字符)'); 服务器向指定的客户端发送消息
           */
          $ws->on('message', function (Server $server, Frame $frame)use ($ws) {
              \Log::info($ws->getClientInfo($frame->fd));#获取绑定的用户信息 记得linux开启storage/logs 写入权限
              $server->push($frame->fd, $frame->data);
              echo $frame->data;
          });
          # 用户路由事件
          $ws->on('request', function (Request $request, Response $response) use ($ws) {
              var_dump($request->get['test']); # 打印控制器,传递过来的参数
              foreach ($ws->connections as $fd) {
                  if ($ws->isEstablished($fd)) {
                      $ws->push($fd, $request->get['test']);
                  }
              }
          });
          /**
           * 关闭事件：客户端断开连接调用的事件
           * $server or $ws  websocket服务器
           * $fd  客户端的唯一编号
           * 不管是关闭客户端还是服务器自己断开。该函数都会执行
           */
          $ws->on('Close', function (Server $server, $fd) {
              echo "client-{$fd} is closed\n";
          });
          # 开启swoole
          $ws->start();
      }
  }
  
  ```
  
  - 开启swoole9*/88
  
   ```php
   php artisan swoole
   # 守护进程启动
   php artisan swoole &    
   ```
  
  ## 控制器事件
  
  > 直接调用此路由就可以传递给command/swoole.php	中request 事件
  >
  > **注意请求的端口一定要和swoole开启的端口一样,ip可以使用 127.0.0.1当作请求本机ip**
  
  ```php
   public function index(Request $request)
     {
          $data = \Http::get('127.0.0.1:1997',['test'=>'888']);
          return;
      }
  ```
  
  # 以面向对象的方式使用websocket
  
  > 创建**PushServer**类 

```php
<?php

class PushServer
{
    private static $instance; # 实例的意思
    private static $server;
    private $messageHandler; # 处理消息的对象

    private function __construct()
    {
        # 创建websocket对象
        self::$server = new Swoole\WebSocket\Server('0.0.0.0', '9502');
        # 注册事件
        self::$server->on('open', [$this, 'onOpen']);# 将当前类里面的onOpen方法作为open的事件处理函数
        # message
        self::$server->on('message', [$this, 'onMessage']);# 将当前类里面的onOpen方法作为open的事件处理函数
        # onWorkerStart
        /**
         * 这里执行加载类只会实例化一次类,如果放在message等中实例化类则会每一次触发都会实例化一次类
         */
        self::$server->on('workerStart', [$this, 'onWorkerStart']);# 将当前类里面的onOpen方法作为open的事件处理函数
        # close
        self::$server->on('close', [$this, 'onClose']);# 将当前类里面的onOpen方法作为open的事件处理函数
    }

    #当客户端连接上之后要执行的方法
    public function onOpen(\Swoole\Server $server, \Swoole\Http\Request $request)
    {
        echo $request->fd; # 打印客户端连接的唯一标识
    }

    # 当客户端向服务器发送消息时要执行的方法
    public function onMessage(\Swoole\Server $server, \Swoole\WebSocket\Frame $frame)
    {
        self::$server->reload();//让其执行onWorkerStart 函数重新加载代码(热更新:这个时候不管你如何修改messageHandler类中的方法都不需要重启swoole)

        // echo $frame->data; //打印客户端的消息(可以发送json数据过来)
         $data=json_decode($frame->data,true);#将收到的json数据转化为数组
        // 传输过来的数据例如  {"cmd":"login","data":"用户A"}}
        if (method_exists($this->messageHandler,$data['cmd'])){
             echo '调用对应方法';
             call_user_func([$this->messageHandler,$data['cmd']],$frame->fd,$data);
         }
    }

    # 客户端和服务器断开连接时执行的方法
    public function onClose(\Swoole\Server $server, $fd)
    {

    }

    public function onWorkerStart()
    {
        echo "onWorkerStart........";
        require_once './MessageHandler.php';
        $this->messageHandler = new MessageHandler();
    }

    public static function getInstance(): PushServer
    {
        if (!self::$instance instanceof self) {
            self::$instance = new self();
        }
        return self::$instance;
    }

    public function start()
    {
        self::$server->start();
    }
}

PushServer::getInstance()->start();
```



**创建MessageHandler类**

```php
<?php

class MessageHandler
{
    /**
     * @param integer $client_id 登录用户唯一编号
     * @param array $data 登录的信息
     */
    public function login($client_id=null,$data=null)
    {
        echo '用户正在登录';
        echo "用户唯一id是:".$client_id;
        echo '456';
        print_r($data);
    }

    public function logout()
    {

    }
}
```

**使用**

```shell
php PushServer.
```









# 补充

## 如何在控制器中请求swoole实例发送消息

###  注意点(注意事项)

> Laravel 是在 FPM 模式下运行的，是**普通 HTTP 请求流程**。它没法直接访问你在 CLI 启动的 Swoole Server 的 `$server` 对象（即 `new Swoole\WebSocket\Server(...)`），即使你 `app('swoole.websocket')` 绑定了一个什么容器，也不是原始 Server 实例。
>
> 所以：你 Laravel 里拿到的 `$swoole` 不是在 CLI 模式启动的，`push` 是不会成功的。



###   方案一(redis方案:  Laravel 写入 Redis / 消息队列，Swoole Server 消费)

这个方案结构优雅、耦合度低、支持集群，是 laravels 实际上也用的方式之一：

1. Laravel HTTP 接收消息
2. 推送消息写入 Redis 列表（或者 Kafka / RabbitMQ 等）
3. WebSocket Server 启动一个定时器/协程，每 0.1 秒去轮询 Redis，获取消息然后推送到对应 fd

**laravel控制器**

```shell
Redis::rpush('ws:push:queue', json_encode([
    'fd' => $fd,
    'message' => $message,
]));
```

**Swoole WebSocket 服务中**

```php
$ws->tick(100, function () use ($ws) {
    while ($data = Redis::lpop('ws:push:queue')) {
        $task = json_decode($data, true);
        if (isset($task['fd'], $task['message']) && $ws->isEstablished($task['fd'])) {
            $ws->push($task['fd'], $task['message']);
        }
    }
});
```

这样一来，Laravel 和 Swoole 分离但可以协作，架构也很优雅，性能也好。

####  laravel+redis+job 实现案例

**架构图快速理解：**

```scss
[Client] ---> [Laravel 控制器] --> [Job Dispatch] --> [Redis list]
                                                   ↓
                                       [Swoole WebSocket Server]
                                     (每 100ms 轮询 Redis 并 push)
```

**Laravel 端配置**

安装 Redis 扩展

```bash
composer require predis/predis
```

创建 Job

```bash
php artisan make:job PushToWebSocket
```

`app/Jobs/PushToWebSocket.php`：

```php
namespace App\Jobs;

use Illuminate\Bus\Queueable;
use Illuminate\Contracts\Queue\ShouldQueue;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Queue\SerializesModels;
use Illuminate\Support\Facades\Redis;

class PushToWebSocket implements ShouldQueue
{
    use InteractsWithQueue, Queueable, SerializesModels;

    protected $fd;
    protected $message;

    public function __construct($fd, $message)
    {
        $this->fd = $fd;
        $this->message = $message;
    }

    public function handle()
    {
        Redis::rpush('ws:push:queue', json_encode([
            'fd' => $this->fd,
            'message' => $this->message,
        ]));
    }
}
```

控制器派发 Job

```php
use App\Jobs\PushToWebSocket;

public function sendMessage(Request $request)
{
    $fd = $request->input('fd');
    $message = $request->input('message');

    PushToWebSocket::dispatch($fd, $message);

    return response()->json(['status' => 'queued']);
}
```

Swoole WebSocket 服务端

启动一个原生 Swoole Server 来消费这个 Redis 队列并推送消息：

 `websocket_server.php` 示例：

```php
<?php

use Swoole\WebSocket\Server;
use Swoole\Http\Request;
use Swoole\WebSocket\Frame;

$ws = new Server("0.0.0.0", 9501);

$ws->on("start", function ($server) {
    echo "WebSocket server started at ws://127.0.0.1:9501\n";
});

$ws->on("open", function (Server $server, Request $request) {
    echo "Connection opened: {$request->fd}\n";
});

$ws->on("message", function (Server $server, Frame $frame) {
    echo "Message received: {$frame->data} from {$frame->fd}\n";
});

$ws->on("close", function ($server, $fd) {
    echo "Connection closed: {$fd}\n";
});

// 开启 Redis 消费定时器
$ws->tick(100, function () use ($ws) {
    $redis = new Redis();
    $redis->connect('127.0.0.1', 6379);

    while ($data = $redis->lPop('ws:push:queue')) {
        $task = json_decode($data, true);

        $fd = $task['fd'] ?? null;
        $message = $task['message'] ?? null;

        if ($fd && $message && $ws->isEstablished((int)$fd)) {
            $ws->push((int)$fd, $message);
            echo "Pushed to fd {$fd}: {$message}\n";
        }
    }
});

$ws->start();
```

最后配置

`.env`

确保你的 `.env` 有 Redis 配置（Laravel 默认这样就行）：

```php
REDIS_HOST=127.0.0.1
REDIS_PORT=6379
```

运行步骤

1. 启动 Laravel 队列 Worker（或者用 `sync` 直接处理 job）：

```shell
php artisan queue:work
```

2. 启动 Swoole WebSocket 服务：

```bash
php websocket_server.php
```

3. 前端连接 WebSocket：`ws://127.0.0.1:9501`
4. 请求 Laravel 控制器：

```bash
curl -X POST http://yourdomain/send-message \
    -d "fd=1&message=hello-from-laravel"
```





### 方案二(多一个swoole Http方案)

#### Swoole Server 上开一个 HTTP 接口

在你的 WebSocket Server 脚本中，加一个 HTTP server，比如这样：

```php
$ws = new Swoole\WebSocket\Server("0.0.0.0", 9501);

$ws->on("start", function ($server) {
    echo "WebSocket Server started\n";
});

$ws->on("open", function ($server, $req) {
    echo "Client connected: {$req->fd}\n";
});

$ws->on("message", function ($server, $frame) {
    echo "Received message from {$frame->fd}: {$frame->data}\n";
});

$ws->on("close", function ($server, $fd) {
    echo "Client {$fd} closed\n";
});

// 开一个 HTTP server 监听推送请求
$http = new Swoole\Http\Server("0.0.0.0", 9502);

$http->on("request", function ($request, $response) use ($ws) {
    $fd = $request->get['fd'] ?? null;
    $message = $request->get['message'] ?? null;

    if ($fd && $ws->isEstablished((int)$fd)) {
        $ws->push((int)$fd, $message);
        $response->end("Pushed to fd $fd");
    } else {
        $response->status(400);
        $response->end("Invalid fd or not connected");
    }
});

$ws->start();
```

这样你就可以在 Laravel 控制器中用 Guzzle 或 curl 调用这个接口：

```php
use Illuminate\Support\Facades\Http;

public function pushMessage(Request $request)
{
    $fd = $request->input('fd');
    $message = $request->input('message');

    $response = Http::get('http://127.0.0.1:9502', [
        'fd' => $fd,
        'message' => $message
    ]);

    return response()->json([
        'status' => $response->successful(),
        'body' => $response->body()
    ]);
}
```

**优点**

Laravel 不用直接访问 Swoole 的 `$server` 对象，避免 CLI 限制

推送逻辑由 Swoole Server 完成，保证可用性

Laravel → HTTP 请求 → Swoole，简单明了

**安全提示**

> ### 安全提示（生产环境时）：
>
> - 加个 token 校验，防止别人乱访问这个 HTTP 接口
> - 可以限制只能本地访问（`127.0.0.1`），或者做个内网保护

###  方案三: (利用swoole  request请求封装)

**swoole服务**

```php
class WebSocketService
{

    public function __construct()
    {
        $ws = new Server('0.0.0.0', 9501); // 设置Swoole服务地址和端口

        // 设置 WebSocket 事件
        $ws->on('open', [$this, 'onOpen']);
        $ws->on('message', [$this, 'onMessage']);

        # 用户路由事件 -在这里面处理
        $ws->on('request', function (Request $request, Response $response) use ($ws) {
            $type = $request->get['type'] ?? "";
            $fd = $request->get['fd'] ?? "";
            $message = $request->get['message'].':request' ?? "";
            if (!empty($type) && $type == 'sendToUid'){
                if ($ws->isEstablished($fd)) {
                    $ws->push($fd,"{$message}");
                }
                if (!$ws->isEstablished($fd)) {
                    \Log::info('print:request'.$fd.':不存在');
                }

            }
        });


        $ws->on('close', [$this, 'onClose']);

//        $tcpServer = $ws->addlistener("0.0.0.0", 9502, SWOOLE_TCP);
//        $tcpServer->on("receive", function ($server, $fd, $reactor_id, $data) {
//            \Log::info('print:'.$data.'----'.$fd.'----'.$reactor_id.PHP_EOL);
//            $payload = json_decode($data, true);
//            if (!$payload || !isset($payload["fd"]) || !isset($payload["message"])) {
//                return;
//            }
//            \Log::info('print:'.'gorun');
//            $uid = $payload["fd"];
//            $message = $payload["message"].'66666666666666';
//            $server->send($uid, $message);
//        });
        $ws->start();//启动
    }
```

**控制器中调用**

```php
        $fd = $request->input("fd");
        $message = $request->input("message");
        if (!$fd || !$message) return response()->json(["error" => "参数fd 或者 message为空"]);
        Http::post('127.0.0.1:9501', ['type' => 'sendToUid', 'fd' => $fd, 'message' => $message]);
        return response()->json(["success" => "消息已发送"]);
```

###  方案四:(把php-fpm请求封装为swoolehttp)

> **记得配置nginx 转发**

#### **旧的代码逻辑(废弃)**

```php
<?php


namespace App\Services;

use Swoole\Http\Server;
use Swoole\Http\Request as SwooleRequest;
use Swoole\Http\Response as SwooleResponse;
use Illuminate\Contracts\Http\Kernel;
use Illuminate\Http\Request;
use Illuminate\Foundation\Application;


class WebSocketService
{
    protected $server;

    public function __construct()
    {
        // 加载 Laravel 项目
        require __DIR__ . '/../../vendor/autoload.php';
        $app = require __DIR__ . '/../../bootstrap/app.php';

// 创建 Laravel Kernel
        $kernel = $app->make(Kernel::class);

// 创建 Swoole HTTP + WebSocket Server
        $server = new \Swoole\WebSocket\Server("0.0.0.0", 9501);

        $server->on("start", function () {
            echo "Swoole HTTP + WebSocket Server started at http://0.0.0.0:9501\n";
        });

        $server->on("request", function (SwooleRequest $req, SwooleResponse $res) use ($app, $kernel, $server) {
            // 初始化 Laravel 请求
            $_SERVER = [];
            foreach ($req->server as $key => $value) {
                $_SERVER[strtoupper($key)] = $value;
            }
            foreach ($req->header ?? [] as $key => $value) {
                $_SERVER['HTTP_' . strtoupper(str_replace('-', '_', $key))] = $value;
            }

            $_GET = $req->get ?? [];
            $_POST = $req->post ?? [];
            $_COOKIE = $req->cookie ?? [];
            $_FILES = $req->files ?? [];

            $content = $req->rawContent(); // 注意！POST body 在这
            $parsedBody = [];
            $contentType = $req->header['content-type'] ?? '';

            if (str_contains($contentType, 'application/json')) {
                $parsedBody = json_decode($content, true);
            } elseif (str_contains($contentType, 'application/x-www-form-urlencoded')) {
                parse_str($content, $parsedBody);
            }



            $_SERVER['REQUEST_URI'] = $req->server['request_uri'] ?? '/';

            foreach ($req->header ?? [] as $key => $value) { // 将所有 HTTP 请求头注入 $_SERVER 变量
                $_SERVER['HTTP_' . strtoupper(str_replace('-', '_', $key))] = $value;
            }


            // 构造 Illuminate Request
            $laravelRequest = Request::createFromBase(
                \Symfony\Component\HttpFoundation\Request::create(
                    $_SERVER['REQUEST_URI'],
                    $_SERVER['REQUEST_METHOD'] ?? 'GET',
                    $parsedBody, // <--- 关键在这
                    $_COOKIE,
                    $_FILES,
                    $_GET,
                    $_SERVER,
                    $content
                )
            );


            // 注入 Swoole Server 到容器，供控制器中使用
            $app->instance('swoole.server', $server);

            // 执行 Laravel
            $laravelResponse = $kernel->handle($laravelRequest);
            $res->status($laravelResponse->getStatusCode());
            foreach ($laravelResponse->headers->allPreserveCase() as $name => $values) {
                foreach ($values as $value) {
                    $res->header($name, $value);
                }
            }
            $res->end($laravelResponse->getContent());

            $kernel->terminate($laravelRequest, $laravelResponse);
        });

// WebSocket 连接事件
        $server->on('open', function ($server, $request) {
            echo "WebSocket client {$request->fd} connected\n";
        });

// WebSocket 消息事件
        $server->on('message', function ($server, $frame) {
            echo "Message from {$frame->fd}: {$frame->data}\n";
            $server->push($frame->fd, "You said: {$frame->data},fd:$frame->fd");
        });

// WebSocket 关闭事件
        $server->on('close', function ($server, $fd) {
            echo "Client {$fd} disconnected\n";
        });

        $server->start();
    }
}
```

####  美化后的swoole服务端

```php
<?php

namespace App\Services;

use Swoole\WebSocket\Server;
use Swoole\Http\Request as SwooleRequest;
use Swoole\Http\Response as SwooleResponse;
use Illuminate\Contracts\Http\Kernel;
use Illuminate\Http\Request;
use Illuminate\Foundation\Application;
use Symfony\Component\HttpFoundation\Request as SymfonyRequest;

class WebSocketService
{
    protected  $server;
    protected  $app;
    protected  $kernel;

    public function __construct()
    {
        $this->initLaravel();
        $this->initWebSocket();
    }

    public function initLaravel(): void
    {
        require base_path('vendor/autoload.php');
        $this->app = require base_path('bootstrap/app.php');
        $this->kernel = $this->app->make(Kernel::class);
    }

    public function initWebSocket(): void
    {
        $this->server = new Server("0.0.0.0", 9501);

        $this->server->on("start", function () {
            echo "Swoole HTTP + WebSocket Server started at http://0.0.0.0:9501\n";
        });


        $this->server->on("request", [$this, 'handleHttpRequest']);
       // $this->server->on("open", [$this, 'onWebSocketOpen']);
        $this->server->on("open", [$this, 'onWebSocketOpen']);
        $this->server->on("message", [$this, 'onWebSocketMessage']);
        $this->server->on("close", [$this, 'onWebSocketClose']);

        $this->server->start();
    }

    public function handleHttpRequest(SwooleRequest $req, SwooleResponse $res): void
    {
        // 转换 Header、Server、Files 等为 Symfony 可识别格式
        $server = $this->formatServer($req->server ?? []);
        $headers = $this->formatHeaders($req->header ?? []);
        //$files = $this->formatFiles($req->files ?? []);
        $__FILES = isset($req->files) ? $req->files : [];
        $content = $req->rawContent();

        // 解析请求体内容
        $parsedBody = $this->parseBody($content, $headers['content-type'] ?? '');

        $symfonyRequest = new SymfonyRequest(
            $req->get ?? [],
            $parsedBody,
            [],
            $req->cookie ?? [],
            $__FILES,
            array_merge($server, $headers),
            $content
        );

        // 构造 Laravel Request
        $laravelRequest = Request::createFromBase($symfonyRequest);

        // 注入 Swoole Server
        $this->app->instance('swoole.server', $this->server);

        $response = $this->kernel->handle($laravelRequest);

        $res->status($response->getStatusCode());

        foreach ($response->headers->allPreserveCase() as $name => $values) {
            foreach ($values as $value) {
                $res->header($name, $value);
            }
        }

        $res->end($response->getContent());

        $this->kernel->terminate($laravelRequest, $response);
    }

    public function onWebSocketOpen(Server $server, $request): void
    {
        echo "WebSocket client {$request->fd} connected\n";
    }

    public function onWebSocketMessage(Server $server, $frame): void
    {
        echo "Message from {$frame->fd}: {$frame->data}\n";
        $server->push($frame->fd, "You said: {$frame->data}, fd:{$frame->fd}");
    }

    public function onWebSocketClose(Server $server, int $fd): void
    {
        echo "Client {$fd} disconnected\n";
    }

    // 工具方法们
    public function formatServer(array $server): array
    {
        $formatted = [];
        foreach ($server as $key => $value) {
            $formatted[strtoupper($key)] = $value;
        }
        return $formatted;
    }

    public function formatHeaders(array $headers): array
    {
        $formatted = [];
        foreach ($headers as $key => $value) {
            $formatted['HTTP_' . strtoupper(str_replace('-', '_', $key))] = $value;
        }
        return $formatted;
    }

//    public function formatFiles(array $files): array
//    {
//        $formatted = [];
//
//        foreach ($files as $key => $file) {
//            if (isset($file['tmp_name'])) {
//                // 单个文件
//                $formatted[$key] = new \Symfony\Component\HttpFoundation\File\UploadedFile(
//                    $file['tmp_name'],
//                    $file['name'],
//                    $file['type'],
//                    $file['size'],
//                    $file['error'],
//                    true
//                );
//            } elseif (is_array($file)) {
//                // 嵌套结构，递归调用
//                $formatted[$key] = $this->formatFiles($file);
//            }
//        }
//
//        return $formatted;
//    }


    public function parseBody(string $content, string $contentType): array
    {
        if (str_contains($contentType, 'application/json')) {
            return json_decode($content, true) ?? [];
        }

        if (str_contains($contentType, 'application/x-www-form-urlencoded')) {
            parse_str($content, $parsed);
            return $parsed;
        }

        return [];
    }
}
```

#### 控制器调用

```php
   public function sendMessage(Request $request)
    {


//        $file=$request->file('file');
//        if($file){
//            $file_name = uniqid() . '@' . $file->getClientOriginalName(); # 定义上传图片得唯一名称
//            $uploadDirectory = '/uploads/' . date('Ymd');  #定义上传文件得路径
//            $savePath = public_path($uploadDirectory);  # 定义上传绝对路径
//            //文件路径如果不存在则自动创建 并赋予权限
//            if (!is_dir($savePath)) mkdir($savePath, 0777, true);
//            $file->move($savePath, $file_name);
//            return response()->json(["file" => $file_name]);
//        }
//        $header=$request->header('demo');
//
//        if($header){
//            return response()->json(["header" => $header]);
//        }



        $fd = $request->input('fd');
        $message = $request->input('message');
        if (!$fd || !$message) return response()->json(["error" => "参数fd 或者 message为空"]);
        $server = app('swoole.server');
        if ($server->isEstablished((int)$fd)) {
            $server->push((int)$fd, $message);
            return response()->json(['status' => 'pushed']);
        } else {
            return response()->json(['status' => 'not connected'], 400);
        }
    }
```

