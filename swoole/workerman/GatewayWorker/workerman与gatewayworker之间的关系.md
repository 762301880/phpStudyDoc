## 一句话先说清楚：

**GatewayWorker 是基于 Workerman 开发的一套“分布式 WebSocket/长连接框架”。**

关系有点像：

- **Workerman** = 底层发动机（通用）
- **GatewayWorker** = 基于这个发动机造出来的“网关服务框架”（更偏业务场景）

类似：

- Go 的 `net/http` → 底层能力
- Gin → 基于它封装的 Web 框架

## Workerman 是什么？

Workerman

它本质是一个 **PHP 常驻内存的 socket 框架**，可以做：

- TCP 服务
- UDP 服务
- HTTP 服务
- WebSocket 服务
- 定时任务
- 长连接服务

比如你可以直接用 Workerman 写一个 websocket 服务：

```php
use Workerman\Worker;

require_once './vendor/autoload.php';

$ws_worker = new Worker("websocket://0.0.0.0:2346");

$ws_worker->onMessage = function($connection, $data) {
    $connection->send("收到: $data");
};

Worker::runAll();
```

客户端连上后：

```php
ws://127.0.0.1:2346
```

## Workerman 的问题

虽然 Workerman 很灵活，但如果你做：

- 聊天室
- IM
- 推送系统
- 游戏长连接
- 多机器部署

你会遇到麻烦：

### 连接和业务耦合

你自己维护：

- 用户 uid 和 connection 的关系
- 房间关系
- 在线状态

例如：

```
$userConnections[$uid] = $connection;
```

用户一多就头疼。

------

### 多进程/多机器通信麻烦

比如：

- 用户 A 在 worker1
- 用户 B 在 worker2

A 发消息给 B：

你得自己解决：

> worker1 怎么找到 worker2 的连接？

如果是多台服务器更麻烦。

## GatewayWorker 是干嘛的？

GatewayWorker

GatewayWorker = 在 Workerman 上封装了一层，专门解决：

- websocket 长连接
- uid绑定
- 群发
- 房间
- 分布式部署
- 跨进程通信

它把系统拆成：

### Gateway

负责：

- 维护客户端连接
- 收发消息

相当于“门卫”。

------

### BusinessWorker

负责：

- 处理业务逻辑

例如：

- 登录
- 聊天
- 下单
- 游戏逻辑

------

### Register

负责：

- Gateway 和 BusinessWorker 的注册中心

类似“通讯录”。

## 架构图（非常重要）

```
客户端
   ↓
Gateway
   ↓
BusinessWorker
   ↓
Redis/MySQL
```

更完整：

```
Client
   ↓
Gateway1 ─┐
Gateway2 ─┼── Register
Gateway3 ─┘

BusinessWorker1
BusinessWorker2
BusinessWorker3
```

------

## 为什么要这样拆？

假设你做聊天室：

100万用户连接进来。

如果用纯 Workerman：

```
连接管理 + 业务逻辑
全混在一起
```

容易炸。

------

GatewayWorker：

```
Gateway → 专门扛连接
BusinessWorker → 专门处理逻辑
```

职责清晰。

比如：

```
Gateway::sendToUid($uid, $message);
```

你甚至不用知道用户连接在哪台机器。

这就是 GatewayWorker 最大价值。

------

## 举个现实例子

### Workerman

像你自己开餐馆：

- 买菜
- 做饭
- 收银
- 上菜

全自己干。

自由，但累。

------

### GatewayWorker

像连锁餐饮系统：

- 前台接单（Gateway）
- 厨房做饭（BusinessWorker）
- 总部协调（Register）

更适合大规模。

------

## 什么时候用哪个？

### 用 Workerman

如果你只是：

- 简单 websocket
- 小型服务
- 定时任务
- TCP 服务

直接 Workerman 就够了。

------

### 用 GatewayWorker

如果你做：

- IM
- 聊天
- 推送
- 游戏
- 多服务器长连接系统

直接 GatewayWorker 更省事。

一句话收尾：

**GatewayWorker ≠ 替代 Workerman，而是 Workerman 的“长连接业务增强版”。**

先学 Workerman 的：

- Worker
- onConnect
- onMessage
- event loop

更像：

Workerman = 操作系统
GatewayWorker = 微信

或者：

Workerman = 毛坯房
GatewayWorker = 精装修