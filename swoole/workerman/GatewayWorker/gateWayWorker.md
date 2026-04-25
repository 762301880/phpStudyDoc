# 一、简介

> GatewayWorker是基于Workerman开发的	
>
> GatewayWorker基于Workerman开发的一个项目框架，用于快速开发长连接应用，例如app推送服务端、即时IM服务端、游戏服务端、物联网、智能家居等等。
> GatewayWorker使用经典的Gateway和Worker进程模型。Gateway进程负责维持客户端连接，并转发客户端的数据给Worker进程处理；Worker进程负责处理实际的业务逻辑，并将结果推>送给对应的客户端。Gateway服务和Worker服务可以分开部署在不同的服务器上，实现分布式集群。
> GatewayWorker提供非常方便的API，可以全局广播数据、可以向某个群体广播数据、也可以向某个特定客户端推送数据。配合Workerman的定时器，也可以定时推送数据。

- 参考资料

| 名称                      | 地址                                                         |
| ------------------------- | ------------------------------------------------------------ |
| GatewayWorker2.x 3.x 手册 | [链接](http://doc2.workerman.net/)                           |
| 安装文档                  | [链接](https://github.com/walkor/GatewayClient)              |
| workerman-后盾人文档      | [链接](https://doc.houdunren.com/%E6%8F%92%E4%BB%B6%E6%89%A9%E5%B1%95/Laravel/4%20workerman.html#%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4) |
| 下载安装                  | [link](https://www.workerman.net/doc/gateway-worker/#GatewayWorker2.x%203.x%20%E6%89%8B%E5%86%8C)   [link](https://www.workerman.net/download/GatewayWorker.zip) |

## 安装

### 先下载服务端

> 可以理解为 php -m  里面的swoole扩展

https://www.workerman.net/download/GatewayWorker.zip

#### 解压

```php
# CentOS / RHEL
yum install unzip -y

# Ubuntu / Debian
apt install unzip -y

# 解压  GatewayWorker
unzip GatewayWorker.zip    
```

#### 启动GatewayWorker

> [参考](https://www.workerman.net/doc/gateway-worker/#GatewayWorker2.x%203.x%20%E6%89%8B%E5%86%8C)
>
> [主要参考](https://www.workerman.net/doc/gateway-worker/work-with-other-frameworks.html)

**linux启动**

```php
php start.php start
```

**windows启动**

```php
p\start_businessworker.php
----------------------- WORKERMAN -----------------------------
Workerman version:4.1.10          PHP version:7.4.3
------------------------ WORKERS -------------------------------
worker                        listen                              processes status
Register             text://0.0.0.0:1238                 1         [ok]
YourAppGateway       websocket://0.0.0.0:8282            1         [ok]
YourAppBusinessWorkernone                                1         [ok]
```

**bug处理**

```php
# 报错
#4 {main}
  thrown in D:\phpstudy_pro\WWW\tp5.1_gateway_worker\GatewayWorker\vendor\workerman\workerman\Worker.php on line 2356"
YourAppGateway       websocket://0.0.0.0:8282            1         [ok]
YourAppBusinessWorkernone                                1         [ok]
process D:\phpstudy_pro\WWW\tp5.1_gateway_worker\GatewayWorker\Applications\YourApp\start_register.php terminated and try to restart
Register             text://0.0.0.0:1238                 1         [ok]
Worker process terminated with ERROR: E_ERROR "Uncaught Exception: 以一种访问权限不允许的方式做了一个访问套接字的尝试。
 in D:\phpstudy_pro\WWW\tp5.1_gateway_worker\GatewayWorker\vendor\workerman\workerman\Worker.php:2356
     
 问题原因 + 一键解决
这个错误 以一种访问权限不允许的方式做了一个访问套接字的尝试 是 Windows 系统专属错误，原因只有一个：GatewayWorker 需要的端口被占用 / 被系统权限阻止，无法监听。
报错的核心是：8282、1238 端口被占用 或 被防火墙 / 权限拦截。    
     
     
# 解决 检查 8282 和 1238 端口被谁占用(如果有输出，说明被其他程序占用，直接用下面命令杀死对应 PID：)
netstat -ano | findstr "8282"
netstat -ano | findstr "1238"
     
# 杀死进程
taskkill /f /pid 这里填查到的PID数字   
```



###   安装 gatewayclient

> https://github.com/walkor/GatewayClient
>
> https://www.workerman.net/doc/gateway-worker/work-with-other-frameworks.html
>
> 类似于 封装后的 swoole 包 可以调用swoole的服务

```php
# 在你的项目中用composer安装

composer require workerman/gatewayclient
```

# 二、使用

## 2.1要使用GatewayWorker首先需要下载GatewayWorker包

[下载地址](https://www.workerman.net/download)

![image-20210609141807052](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210609141807052.png)

## 2.2 解压到项目中

- 可以放在任意位置中
- windows请双击使用`start_for_win.bat`启用项目

## 2.3重要的事情说三遍

业务开发只需要关注 Applications/项目/Events.php一个文件即可。
业务开发只需要关注 Applications/项目/Events.php一个文件即可。
业务开发只需要关注 Applications/项目/Events.php一个文件即可。

## 2.4 开发需要在框架的Events中改写代码以便结合框架使用

- 此处请参考官方手册[与ThinkPHP等框架结合](http://doc2.workerman.net/work-with-other-frameworks.html)

```php
public static function onConnect($client_id)
    {
        Gateway::sendToClient($client_id, json_encode(array(
        'type'      => 'init',
        'client_id' => $client_id
     )));
    }
```

- 流程

>  前端在此处连接websocket然后,后端会返回一条json数据前端，前端使用switch判断是否已经初始化，
>
> 如果返回数据有init则代表连接websocket成功，此时前端需要拿到传输过来的client_id与用户表中的用户
>
> 表中的id进行绑定，也就是与用户进行绑定，此时前段用返回的数据进行switch判断如果是init则请求后端的
>
> 绑定用户数据接口，前端需要把拿到的client_id传输过去,

例子:

- 路由

```php
Route::get('bindUid','BindUidController@bind');
```

- 控制器

> 这里先绑定前端传输过来的client_id与数据库中的id,再将用户的数据传输给前端,
>
> $data里的type是用于前段判断是某种类型

```php
use GatewayClient\Gateway;
use Illuminate\Http\Request;
public function __construct()
{
     //设置Gatewayworker服务的register服务与ip和端口
     Gateway::$registerAddress='127.0.0.1:8282';
}
public function bind(Request $request)
{
  $uid='用户id';//获取用户id赋值给变量
  $client_id=$request->get('client_id');
  Gateway::bindUid($client_id,$uid);//绑定用户id  参数1 前端传输过来的clientid 参数2 需要绑定的当前的用户id
   $data = [
            'type' => 'type',
            'data' => [
                '用户信息1' => '用户信息1',
                '用户信息2' => '用户信息2',
                '用户信息3' => '用户信息3'
            ]
        ];
    //绑定完成之后再将用户的数据发送给前端  
    Gateway::sendToAll(json_encode($data));  
}
```



# [tp5 Workerman 快速上手手册](https://www.kancloud.cn/thinkphp/think-worker/722897#Workerman_Server_389)





# 补充

##  gatewayworker 与 gatewayworker/gatewayclient什么关系

**`gatewayworker/gatewayclient` 不是给 Gateway 服务内部用的。**
 它是给 **“外部 PHP 项目”远程调用 GatewayWorker** 用的。

关系像这样：

```php
浏览器/App
    ↓
Gateway
    ↓
BusinessWorker

你的 Laravel/ThinkPHP/Hyperf 后台
    ↓
GatewayClient
    ↓
GatewayWorker
```

### 正常情况下（只用 GatewayWorker）

GatewayWorker 自己内部就能完成：

- client连接
- 消息转发
- uid绑定
- 群发

例如：

```php
Gateway::sendToUid($uid, $msg);
```

BusinessWorker 内部直接调用就行。

### 那 GatewayClient 是干嘛的？

GatewayClient 的作用是：

> **让外部项目也能操作 GatewayWorker**

比如你有：

- Laravel 后台
- ThinkPHP 后台
- Hyperf 服务
- 普通 PHP 接口

用户下单成功后，你想主动 websocket 推送：

> "你的订单已支付"

但你的订单代码不在 GatewayWorker 里，而是在 Laravel/Hyperf 里。

这时候：

```php
composer require workerman/gatewayclient
```

然后：

```php
GatewayClient\Gateway::$registerAddress = '127.0.0.1:1238';

GatewayClient\Gateway::sendToUid($uid, '支付成功');
```

它会连接 Register，再找到对应 Gateway，把消息推过去。

###  为什么不直接用 GatewayWorker？

因为你的业务系统可能是：

- HTTP 接口项目
- Admin 后台
- 定时任务系统

它们并不是 GatewayWorker 进程。

不能直接调用：

```
Gateway::sendToUid()
```

因为：

- 没有启动 GatewayWorker runtime
- 不在它的进程环境内

所以需要一个“远程客户端”。

### 类比一下

GatewayWorker 像酒店：

- Gateway = 前台
- BusinessWorker = 服务员
- Register = 总机

而 GatewayClient：

像外卖电话。

你人在酒店外面：

> “帮我给 302 房送瓶水”

酒店内部完成配送。

### 典型场景

比如：

用户 websocket 在线

同时你有个后台管理系统：

管理员封号用户：

```php
GatewayClient\Gateway::sendToUid($uid, "你已被封禁");
```

或者：

定时任务：

```php
GatewayClient\Gateway::sendToAll("系统维护通知");
```

所以完整关系是：

```php
Workerman
   ↓
GatewayWorker
   ↓
GatewayClient（给外部系统调用）
```

###   最核心一句话

> **GatewayClient 是 GatewayWorker 提供的“远程控制客户端 SDK”**

###  它们的真实关系

GatewayWorker
 = 长连接服务端（跑 websocket / TCP / 连接管理）

GatewayClient
 = 外部 PHP 项目用来“远程调用 GatewayWorker 的工具包”

## GatewayWorker 一般部署到哪里

❌ GatewayWorker 不应该“放进某个业务项目文件夹里当子模块跑”
 ✅ 正确做法是：**独立部署成一个常驻服务进程**

## GatewayWorker 应该放哪里？

GatewayWorker 本质是：

> 一个长期运行的 **WebSocket / TCP 服务集群**

所以它通常部署在：

###  1. 独立服务器（最常见）

```php
服务器A：
  ├── Nginx / PHP-FPM（业务接口）
  ├── MySQL / Redis
  └── GatewayWorker（常驻进程）
```

### 2. 同一台服务器，但独立进程运行

```php
/var/www/api（Laravel/TP项目）
/var/www/gatewayworker（独立目录）
```

启动方式：

```php
php start.php start -d
```

### ❌ 不推荐方式（放在业务代码里）

```
/A项目
   ├── controller/
   ├── model/
   ├── gatewayworker/   ❌ 放在业务代码里
```

问题：

- 容易误当“框架模块”
- 部署混乱
- 业务代码 reload 会影响 websocket
- 不能独立扩容

🧩 正确架构（标准工业写法）

```php
        用户浏览器
             ↓
        WebSocket连接
             ↓
     GatewayWorker（独立服务）
             ↓
   Redis / MQ / DB
             ↓
 Laravel / ThinkPHP / Hyperf
```

##  为什么必须独立部署？

因为 GatewayWorker 是：

- 常驻内存
- 长连接
- 多进程
- 不走 HTTP 请求生命周期

而你的 A 项目（Laravel/TP）是：

- PHP-FPM
- 请求-响应模式
- 用完即销毁

👉 这俩生命周期完全不同

# 注意事项

## thinkphp5.1为什么有gateway_worker.php和worker_server.php

> 这两个配置文件依赖于 **composer require topthink/think-worker=2.0.***  [tp5.1使用workerman文档](https://doc.thinkphp.cn/v5_1/Workerman.html)

gateway_worker.php 等价于 gatewayworker 所以 不用安装 gatewayworker 直接启动 **worker:gateway**

启动websocket服务就行

worker_server.php 一般跑定时任务  不做websocket



##  gatewayclient 是什么

用电视机遥控器比喻的话

gatewayclient 就是遥控器

gatewayworker  就是电视机

##  只安装了gatewayclient laravel如何调用websocket的 onClose事件呢

> gatewayclient 只是调用了 gatewayworker的接口 这时候想做断连事件就很麻烦
>
> 在 Laravel 中安装了 `gatewayclient`，这是 **GatewayWorker** 的客户端扩展包，专门用来和 GatewayWorker 服务端通信，实现 WebSocket 非常简单。

### 解决方案一

> Laravel 无法监听 WebSocket 断开事件，**只能在 GatewayWorker 里处理**。
>
> 这是你必须运行的 **GatewayWorker 服务端 start.php**，直接复制覆盖：

```php
<?php
use GatewayWorker\Gateway;
use GatewayWorker\BusinessWorker;
use GatewayWorker\Register;
use Workerman\Worker;
use GatewayClient\Gateway;

// 注册服务
$register = new Register('text://0.0.0.0:1238');

// 业务处理
$worker = new BusinessWorker();
$worker->name = 'BusinessWorker';
$worker->count = 4;
$worker->registerAddress = '127.0.0.1:1238';

// 网关
$gateway = new Gateway("websocket://0.0.0.0:8282");
$gateway->name = 'Gateway';
$gateway->count = 4;
$gateway->lanIp = '127.0.0.1';
$gateway->startPort = 2900;
$gateway->registerAddress = '127.0.0.1:1238';

// ==== 客户端连接时触发 ====
$gateway->onConnect = function($connection) {
    echo "客户端连接：{$connection->id}\n";
};

// ==== 客户端断开连接时触发（你要的 onClose）====
$gateway->onClose = function($connection) {
    $clientId = $connection->id;

    // ==============================================
    // 在这里写断开后的逻辑！！！
    // ==============================================
    echo "客户端断开：{$clientId}\n";

    // 1. 获取这个 clientId 绑定的 uid
    $uidList = Gateway::getUidByClientId($clientId);
    if ($uidList) {
        $uid = $uidList[0];
        echo "用户 {$uid} 断开连接\n";

        // 你可以在这里：
        // - 修改数据库用户状态为离线
        // - 通知其他用户此人下线
    }
};

// === 前端发消息时触发 ===
$gateway->onMessage = function($connection, $data) {
    // 前端发过来的消息 $data
    // 你可以在这里处理心跳、鉴权、绑定 uid
};

Worker::runAll();
```

#### 最关心的：断开时如何操作 Laravel 数据库？

99% 的人需要：

**客户端断开 → 修改用户状态为离线**

#### 方案 A：在 GatewayWorker 里直接连数据库（最简单）

在 `onClose` 里直接写 MySQL：

```php
$gateway->onClose = function($connection) {
    $clientId = $connection->id;
    $uid = Gateway::getUidByClientId($clientId);

    if ($uid) {
        // 直接操作数据库
        $pdo = new PDO('mysql:host=127.0.0.1;dbname=laravel_db', 'root', '');
        $pdo->prepare("UPDATE users SET is_online=0 WHERE id=?")->execute([$uid]);

        echo "用户 $uid 已下线\n";
    }
};
```

#### 方案 B：断开时调用 Laravel API（推荐）

> 让 GatewayWorker 通知 Laravel 做业务处理：

```php
$gateway->onClose = function($connection) {
    $clientId = $connection->id;
    $uid = Gateway::getUidByClientId($clientId);

    if ($uid) {
        // 调用 Laravel 接口
        $url = "http://127.0.0.1:8000/api/user/offline?uid=$uid";
        file_get_contents($url);
    }
};
```

然后 Laravel 路由：

```php
Route::get('/api/user/offline', [UserController::class, 'offline']);
```

#### 方案C redis队列不耦合(超级推荐)

终极方案：**Redis 订阅 / 推送**（行业标准）

原理（一句话）

1. **GatewayWorker 断开时 → 推一条消息到 Redis**
2. **Laravel 监听 Redis → 接收断开事件**
3. **全优雅、无耦合、不跨项目、不调 HTTP**

这是 **大厂通用架构**。

GatewayWorker 服务端（独立，不装 Laravel）

只做一件事：**用户断开 → 推送到 Redis**

不写业务、不连数据库、不触发事件

```php
<?php
require __DIR__.'/vendor/autoload.php';

use GatewayWorker\Gateway;
use GatewayWorker\BusinessWorker;
use GatewayWorker\Register;
use Workerman\Worker;
use GatewayClient\Gateway;
use Predis\Client;

// 注册服务
$register = new Register('text://0.0.0.0:1238');

$worker = new BusinessWorker();
$worker->registerAddress = '127.0.0.1:1238';

$gateway = new Gateway("websocket://0.0.0.0:8282");
$gateway->registerAddress = '127.0.0.1:1238';

// Redis 客户端
$redis = new Predis\Client([
    'host' => '127.0.0.1',
    'port' => 6379,
]);

// 客户端断开
$gateway->onClose = function($conn) use ($redis) {
    $clientId = $conn->id;
    $uid = Gateway::getUidByClientId($clientId);

    if (!$uid) return;

    // ==========================
    // 只推送事件到 Redis
    // ==========================
    $redis->publish('websocket:events', json_encode([
        'event' => 'client_closed',
        'uid' => $uid,
        'client_id' => $clientId,
    ]));
};

Worker::runAll();
```

**laravel端处理**

创建 Laravel 命令：监听 Redis

```php
php artisan make:command WebSocketEventListener
```

写监听代码（优雅处理断开）

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Predis\Client;
use App\Models\User;

class WebSocketEventListener extends Command
{
    protected $signature = 'ws:listen';

    public function handle()
    {
        $redis = new Client([
            'host' => config('database.redis.default.host'),
            'port' => config('database.redis.default.port'),
        ]);

        echo "WebSocket 事件监听中...\n";

        // 订阅 Redis 通道
        $redis->subscribe(['websocket:events'], function ($msg) {
            $data = json_decode($msg, true);
            
            if ($data['event'] === 'client_closed') {
                $uid = $data['uid'];
                
                // ==============================
                // ✅ 在这里优雅处理断开！
                // ==============================
                User::where('id', $uid)->update(['online' => 0]);
                \Log::info("用户 $uid 已离线");
            }
        });
    }
}
```

Laravel 监听（独立进程）

```php
php artisan ws:listen
```

✅ 完全解耦

- GatewayWorker 只做 WebSocket
- Laravel 只做业务
- **不互相安装、不互相侵入**

✅ 没有 HTTP 调用

- 不跨项目请求
- 不延迟、不损耗性能
- 纯内存速度

代码干净到极致

- GatewayWorker 无业务代码
- Laravel 用模型、用事件、用日志
- **完全符合你的架构审美**

可分布式部署

- GatewayWorker 放一台服务器
- Laravel 放一台服务器
- Redis 共用





### 为什么 Laravel 不能监听 onClose？

我给你讲清楚原理，你以后就不会踩坑：

- **GatewayWorker = WebSocket 服务端**（负责连接、断开、事件）
- **GatewayClient = 发送消息工具**（只负责发数据，不负责监听）
- **Laravel = Web 框架**（HTTP 无状态，无法长连接）

所以：

- **onConnect /onClose/onMessage → 必须写在 GatewayWorker**
- **发送消息 → 可以在 Laravel 用 GatewayClient**

