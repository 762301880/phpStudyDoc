#  一、资料

| 资料名称       | 资料地址                                         |
| -------------- | ------------------------------------------------ |
| swoole入门指引 | [链接](https://wiki.swoole.com/wiki/page/1.html) |
| swoole官方文档 | [链接](https://wiki.swoole.com/#/)               |
|                |                                                  |

# 二、laravel中使用swoole

## 1.1 创建command

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
          //$this->ws->on('open', [$this, 'open']);
          # 用户连接事件
          $ws->on('open', function (Server $server, Request $request) {
              $server->bind($request->fd,'401');# 绑定用户 第二个参数暂时写死
              echo "server: handshake success with fd{$request->fd}\n";
          });
          # 用户发送消息事件
          $ws->on('message', function (Server $server, Frame $frame)use ($ws) {
              \Log::info($ws->getClientInfo($frame->fd));#获取绑定的用户信息 记得linux开启storage/logs 写入权限
              $server->push($frame->fd, $frame->fd);
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

  

  ## 1.2 控制器事件

  > 直接调用此路由就可以传递给command/swoole.php	中request 事件

  ```php
   public function index(Request $request)
     {
          $data = \Http::get('www.cs.com:1997',['test'=>'888']);
          return;
      }
  ```

  