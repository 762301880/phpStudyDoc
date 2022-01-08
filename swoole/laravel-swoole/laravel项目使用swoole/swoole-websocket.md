# 资料

| 资料名称       | 资料地址                                         |
| -------------- | ------------------------------------------------ |
| swoole入门指引 | [链接](https://wiki.swoole.com/wiki/page/1.html) |
| swoole官方文档 | [链接](https://wiki.swoole.com/#/)               |
| swoole在线测试 | [链接](http://coolaf.com/tool/chattest)          |

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
          //$this->ws->on('open', [$this, 'open']);
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

















