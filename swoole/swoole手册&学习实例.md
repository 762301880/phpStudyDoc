# 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方资料 | [swoole 手册](https://www.kancloud.cn/fage/swoole_extension/691334)  php中文网[swoole技巧](https://www.php.cn/swoole/)  laravel学院[swoole](https://laravelacademy.org/search?term=swoole)  [实战技巧-第三方博客](https://www.kancloud.cn/wangking/hyperf/2005902) |

# tcp

## [tcp服务端](https://wiki.swoole.com/#/start/start_tcp_server)

> 创建**server.php**

```php
<?php

/**
 * https://wiki.swoole.com/wiki/page/p-server.html
 */
# 构建Server对象
$serv=new Swoole\Server('0.0.0.0','9501'); #host:0.0.0.0 允许外网连接,127.0.0.1 只允许内网连接

# 设置运行时参数 https://wiki.swoole.com/wiki/page/275.html

$serv->set(array(
    'worker_num' => 4, # 设置启动的Worker进程数。
    'daemonize' => false, # 守护进程化。设置daemonize => 1时，程序将转入后台作为守护进程运行。长时间运行的服务器端程序必须启用此项 后台运行
    'backlog' => 128, # Listen队列长度，如backlog => 128，此参数将决定最多同时有多少个等待accept的连接。
));
/**
 * 注册事件回调
 * 事件回调函数 https://wiki.swoole.com/wiki/page/41.html
 */
# 连接事件
$serv->on('Connect', function (){
    echo 'collect';
});
$serv->on('Receive', function (){
    echo '465';
});
$serv->on('Close', function (){
    echo 'close';
});
$serv->start();
```

**运行**

```php
php server.php
# 查询是否启用成功
netstat -anp | grep 9501    

# 尝试连接tcp服务    
# windows 安装telnet     https://jingyan.baidu.com/article/b7001fe16d559c4f7282ddbf.html
apt -y install telnet
telnet 127.0.0.1 9501
```

## [tcp客户端](https://wiki.swoole.com/wiki/page/p-client.html)

> 建立**client.php**

## 实战演练

### [计算在线人数](https://blog.csdn.net/haibo0668/article/details/118193894)

> 请自行添加 message，Close 事件 [bug参考](https://blog.csdn.net/thinkthewill/article/details/109034237)

```php
# 用户连接事件 用户连接的时候返回一个连接通知
use Swoole\WebSocket\Server;
use Swoole\Http\Request;
use Swoole\Timer;
  $ws->on('open', function (Server $server, Request $request) {
            # 使用定时任务推送
            Timer::tick(1000,function ()use ($server,$request){
                if ($server->isEstablished($request->fd)){ #检查连接是否为有效的 WebSocket 客户端连接。
                    $server->push($request->fd, '当前在线人数' . count($server->connections));
                }
            });
        });
```

[**简单聊天室功能**](https://blog.csdn.net/weixin_41423450/article/details/82975346)

[参考](https://blog.csdn.net/jz_Orange/article/details/108366851?utm_medium=distribute.pc_aggpage_search_result.none-task-blog-2~aggregatepage~first_rank_ecpm_v1~rank_v31_ecpm-6-108366851.pc_agg_new_rank&utm_term=swoole+%E5%AE%9E%E7%8E%B0%E8%81%8A%E5%A4%A9%E5%8A%9F%E8%83%BD&spm=1000.2123.3001.4430)

> 请自行添加其他事件

```php
# 用户发送消息事件
        $ws->on('message', function (Server $server, Frame $frame) use ($ws) {
            foreach ($ws->connections as $fd){
            # $ws->getClientInfo($frame->fd) 可以利用这个函数获取绑定人的名称等信息
                if ($frame->fd == $fd){
                    # 返回自己发送的消息
                    $server->push($fd,  '我对大家说'.$frame->data);
                }else{
                    # 推送给所有人发送的消息 
                    $server->push($fd,  $fd.$frame->data);
                }
            }
        });
```

# udp

## udp调试

### [安装 netcat](https://eternallybored.org/misc/netcat/)

> https://blog.csdn.net/walykyy/article/details/105982540

# 协程

## 🧠 一句话理解「协程」

> 协程就是**更轻量级的线程**，可以**节省资源**，让多个任务「看起来像是同时执行的」。
>
> 它可以在 **一个线程里切换多个任务**，不像传统多线程那样占用更多内存或 CPU。

## ✅ 使用 Swoole 协程的好处

- 提升性能：高并发处理能力强
- 更节省资源：比多线程、进程更省内存
- 编写方式更接近同步代码，**易读易写**

## 📦 Swoole 协程环境准备

使用前请确认你安装了 Swoole（推荐安装 `swoole` 扩展）：

```bash
pecl install swoole
```

------

## 📘 例子：用协程发起两个 HTTP 请求（同时进行）

```php
<?php
// 引入 Swoole 协程的 HTTP 客户端
use Swoole\Coroutine\Http\Client;

Swoole\Coroutine\run(function () {
    // 创建两个协程，分别发起两个请求
    go(function () {
        $cli = new Client('jsonplaceholder.typicode.com', 80);
        $cli->get('/todos/1');
        echo "任务1返回：\n";
        echo $cli->body . PHP_EOL;
        $cli->close();
    });

    go(function () {
        $cli = new Client('jsonplaceholder.typicode.com', 80);
        $cli->get('/todos/2');
        echo "任务2返回：\n";
        echo $cli->body . PHP_EOL;
        $cli->close();
    });
});
```

------

## 🔍 注释说明

```php
Swoole\Coroutine\run(function () {
```

- 启动一个协程环境，所有协程要在它里面运行

```php
go(function () {
```

- 创建一个新的协程，相当于开启一个「轻量线程」

```
$cli = new Client('jsonplaceholder.typicode.com', 80);
$cli->get('/todos/1');
```

- 使用协程 HTTP 客户端发请求，这里的请求是**非阻塞的**，不影响其他协程执行

```php
echo $cli->body;
```

- 打印返回的数据，每个协程独立工作

------

## 🧪 输出效果（两个请求几乎同时返回）：

```php
复制编辑任务1返回：
{...内容1...}

任务2返回：
{...内容2...}
```

------

## 🧰 使用场景举例

Swoole 协程适合这些场景：

| 场景                 | 举例                            |
| -------------------- | ------------------------------- |
| ⏱️ 高并发请求         | 短视频接口、订单系统等          |
| 🌐 多个 HTTP 请求     | 聚合多个接口（如聚合天气+物流） |
| 🗃️ 多个数据库操作     | 同时查多个表或数据库            |
| 📨 多个 Redis/MQ 操作 | 异步写入缓存或消息队列          |



------

## 🧠 小结

| 优点   | 说明                     |
| ------ | ------------------------ |
| 🚀 快   | 比传统阻塞式代码快很多   |
| 🧵 轻   | 比线程更省资源           |
| 👶 简单 | 写起来像同步代码，易维护 |

## 🧠go  是不是等于    Coroutine::create



是的，**`go` 和 `Swoole\Coroutine::create()` 本质上是等价的**，它们都是用来**创建一个新的协程（Coroutine）**。
 不过它们之间有一些**细节差异和推荐使用方式**，我来详细给你解释一下。

------

## ✅ go vs Coroutine::create 对比

| 对比点         | `go`             | `Swoole\Coroutine::create()` |
| -------------- | ---------------- | ---------------------------- |
| 🧠 简洁性       | ✅ 更简洁，推荐用 | 比较长                       |
| 📦 是否是语法糖 | 是               | 否                           |
| 🔧 功能差异     | 基本一致         | 基本一致                     |
| 🧾 使用方式     | 全局函数，写法短 | 类静态方法，写法长           |



------

## 🔍 示例对比

### ✅ `go` 写法（推荐）

```
php复制编辑go(function () {
    echo "Hello from go()\n";
});
```

### 🆚 `Coroutine::create()` 写法

```
php复制编辑Swoole\Coroutine::create(function () {
    echo "Hello from Coroutine::create()\n";
});
```

------

## 🧠 为啥推荐用 `go()`？

因为：

- 写起来更短、更清爽
- 在 Swoole 官方文档中也推荐用 `go()`，它就是为了更方便地写协程
- 后续维护更方便，写协程的地方一眼就能看出来

------

## 🎯 总结一句话

> ✅ `go()` 就是 `Swoole\Coroutine::create()` 的语法糖，作用相同，但写法更简洁，推荐使用！

------

如果你还想了解其他协程相关函数，比如 `sleep()`、`defer()`、`channel`，我也可以继续讲给你听！



##### 您说：

线程与协程的区别



###### ChatGPT 说：

这个问题非常关键！很多人刚接触协程时都会好奇：“协程和线程到底有什么区别？”
 不用担心，我用**通俗易懂 + 举例 + 表格总结**的方式，帮你彻底搞明白👇

------

## 🧠 一句话理解

| 名称     | 通俗理解                   |
| -------- | -------------------------- |
| **线程** | 操作系统创建的“干活的工人” |
| **协程** | 程序自己管理的“轻量工人”   |



线程是由 **操作系统管理** 的；
 协程是由 **程序自己调度** 的（比如 Swoole 就是自己调度协程）。

------

## 🎯 主要区别一览表

| 对比项     | 线程（Thread）                     | 协程（Coroutine）                  |
| ---------- | ---------------------------------- | ---------------------------------- |
| 💡 创建成本 | 高（操作系统开销大）               | 低（非常轻量）                     |
| 🧠 切换成本 | 高（上下文切换由系统管理）         | 低（用户态切换）                   |
| 📦 内存占用 | 多（每个线程栈空间较大）           | 少（每个协程几 KB）                |
| 🛠️ 控制权   | 操作系统调度                       | 程序自己调度（比如 Swoole）        |
| 📶 并发数量 | 数百~上千个受限                    | 成千上万甚至更多                   |
| ❌ 阻塞影响 | 会影响其他线程                     | 不会（协程是非阻塞的）             |
| 🧵 执行方式 | 并发/并行（多核 CPU 真正同时执行） | 并发（看起来像同时执行，实则轮流） |



------

## 🎨 举个生活中的例子

### 🧵 线程像什么？

> 线程就像你开了很多个“真正的员工”去干活，每人一套工位、电脑，互不干扰。但人多了资源就不够用了。

### 🌀 协程像什么？

> 协程就像你只有一个员工，但他非常高效，会合理切换任务，比如：这个任务等数据时，就立刻切另一个任务继续做，不浪费时间。

------

## 📘 程序中的例子（对比）

### 用线程处理 1000 个请求

```
php复制编辑for ($i = 0; $i < 1000; $i++) {
    // 每个请求都开一个线程，会吃掉很多资源
    // 并且线程切换开销大
    new Thread(function () {
        doSomething();
    });
}
```

### 用协程处理 1000 个请求（Swoole）

```
php复制编辑Swoole\Coroutine\run(function () {
    for ($i = 0; $i < 1000; $i++) {
        go(function () {
            doSomething();
        });
    }
});
```

- 协程方式几乎不占用额外资源，**可以支持非常高的并发**。
- 而线程方式到了几百个就会感觉系统压力大、慢甚至崩溃。

------

## 🧪 小结记忆口诀

> “协程轻，线程重；协程快，线程慢。协程是聪明的切换，线程是笨重的切换。”

------

## 🔧 最后提一句

- 协程适合 I/O 密集型任务（网络请求、数据库、文件等）
- 线程/进程更适合 CPU 密集型任务（图像处理、大量计算）

## 协程案例

### **使用场景举例**

Swoole 协程特别适用于以下场景：

- 并发请求接口（如同时请求多个第三方接口）
- 并发数据库查询
- 并发文件 IO
- 并发 Redis 操作
- 高频、低延迟的 TCP/UDP 通信

###  **代码1 说明**

> 使用 `microtime(true)` 获取当前时间戳（秒级带毫秒）
>
> 使用 `Swoole\Coroutine\WaitGroup` 等待多个协程完成，保证总耗时准确
>
> 输出为：
>
> ```bash
> Result 0: array(1) { ... }
> Result 1: array(1) { ... }
> Result 2: array(1) { ... }
> 耗时: 1.003 秒  
> ```
>
> 如果没有使用协程则运行时间为3秒

> ### 输出说明：
>
> 这段代码中，我们用 `go()` 启动多个协程，并发去请求多个网站的数据，不会阻塞主进程。相比传统同步请求，它速度更快，资源占用更低。

```php
<?php

use Swoole\Coroutine;
use Swoole\Coroutine\MySQL;

Co\run(function () {
    $start = microtime(true); // 开始时间

    $wg = new Coroutine\WaitGroup(); //等待多个协程完成，保证总耗时准确

    for ($i = 0; $i < 3; $i++) {
        $wg->add();
        go(function () use ($i, $wg) {
            $mysql = new MySQL();
            $mysql->connect([
                'host' => '113.45.29.83',
                'user' => 'root',
                'password' => '123456',
                'database' => 'laravel_study',
                'port' => 3307
            ]);

            $result = $mysql->query("SELECT SLEEP(1), 'Query $i'");
            echo "Result $i: ";
            var_dump($result);
            $wg->done();
        });
    }

    $wg->wait(); // 等待所有协程完成
    $end = microtime(true); // 结束时间
    echo "耗时: " . round($end - $start, 3) . " 秒\n";
});
```

## 补充

### swoole 线程 协程 进程之间的关系

Swoole 是一个高性能的 PHP 异步并发框架，它支持多种并发模型，包括线程、协程和进程。下面是它们之间的关系和区别：

- **进程 (Process)**
  - 每个进程都有独立的内存空间。
  - 进程间的通信通常通过 IPC（Inter-Process Communication）机制实现，如管道、消息队列、共享内存等。
  - 在 Swoole 中，可以通过 `swoole_process` 创建子进程，适用于需要完全隔离的场景。

- **线程 (Thread)**
  - 同一进程内的多个线程共享内存空间。
  - 线程间的通信相对简单，可以直接访问共享变量。
  - 在 Swoole 中，可以通过多线程模式启动服务器，每个工作线程处理不同的连接请求。

- **协程 (Coroutine)**
  - 协程是一种轻量级的用户态线程，不依赖操作系统调度。
  - 协程之间通过协作的方式切换执行，提高了并发性能。
  - 在 Swoole 中，协程可以用于异步 I/O 操作，使得代码更加简洁高效。

**关系总结**

1. **进程 vs 线程**：
   - 进程是操作系统分配资源的基本单位，每个进程有独立的内存空间。
   - 线程是进程内的执行单元，共享同一进程的内存空间。
   - 进程间通信复杂，线程间通信简单。

2. **协程 vs 线程**：
   - 协程是用户态的轻量级线程，不依赖操作系统调度。
   - 协程通过协作方式切换，线程通过操作系统调度。
   - 协程适用于高并发场景，线程适用于需要并行计算的场景。

3. **Swoole 中的应用**：
   - **多进程模式**：适用于需要完全隔离的场景，如不同的业务逻辑。
   - **多线程模式**：适用于需要并行处理的场景，如 CPU 密集型任务。
   - **协程模式**：适用于高并发 I/O 操作，如网络请求、数据库操作。

通过合理选择和组合这些并发模型，可以充分发挥 Swoole 的性能优势。

# swoole-Table([高性能共享内存 Table](https://wiki.swoole.com/zh-cn/#/memory/table?id=高性能共享内存-table))

## 1. **Swoole\Table 是什么？**

`Swoole\Table` 是 Swoole 提供的一个**内存共享数据结构**，专门用于多进程/多协程之间共享数据。
 特点：

- **常驻内存**（只要 Master 进程不退出，数据就一直在）
- **支持多进程并发读写**（内部基于内存锁）
- **支持行、列数据存储**（类似内存数据库）
- **非常快**（底层用 C 实现）

常用场景：

- 存储用户在线状态（聊天室、WebSocket）
- 缓存数据（排行榜、计数器、限流）
- 进程间共享配置信息

------

## 2. **基本用法**

### 创建 Table

```php
<?php
$table = new Swoole\Table(1024); // 1024 行（最大行数，必须是 2 的次方）

// 定义列
$table->column('id', Swoole\Table::TYPE_INT, 4);      // 整数型，占 4 字节
$table->column('score', Swoole\Table::TYPE_FLOAT);    // 浮点型
$table->column('name', Swoole\Table::TYPE_STRING, 64);// 字符串，最大长度 64

$table->create(); // 创建表
```

------

### 增 / 改 数据

```php
$table->set('user_1', [
    'id'    => 1,
    'score' => 99.5,
    'name'  => 'Tom'
]);

// 或者单独设置某列
$table->set('user_1', ['score' => 100]);
```

------

### 查 数据

```php
$data = $table->get('user_1');
print_r($data); // Array ( [id] => 1 [score] => 100 [name] => Tom )

// 获取单列
echo $table->get('user_1', 'score'); // 100
```

------

### 删 数据

```php
$table->del('user_1');
```

------

### 遍历数据

```php
foreach ($table as $key => $row) {
    echo "Key: {$key}, Name: {$row['name']}, Score: {$row['score']}\n";
}
```

------

## 3. **数据类型和大小**

`column()` 定义列时的类型：

- `Swoole\Table::TYPE_INT`（整数）
- `Swoole\Table::TYPE_FLOAT`（浮点数）
- `Swoole\Table::TYPE_STRING`（字符串）

**注意事项**：

- `TYPE_INT` 要指定字节大小（1, 2, 4, 8）
- `TYPE_STRING` 要指定最大长度（必须 <= 64KB）
- 表的行数必须是 **2 的 N 次方**（比如 1024, 2048, 4096）

------

## 4. **在多进程中使用**

`Swoole\Table` 必须在 **Server 启动前** 创建好，然后作为全局变量传给 Worker。

```php
use Swoole\Table;
use Swoole\WebSocket\Server;

$table = new Table(1024);
$table->column('fd', Table::TYPE_INT, 4);
$table->create();

$server = new Server("0.0.0.0", 9501);

// 新连接
$server->on('open', function ($server, $req) use ($table) {
    $table->set($req->fd, ['fd' => $req->fd]);
    echo "用户 {$req->fd} 连接\n";
});

// 收消息
$server->on('message', function ($server, $frame) {
    echo "收到消息: {$frame->data}\n";
});

// 关闭连接
$server->on('close', function ($server, $fd) use ($table) {
    $table->del($fd);
    echo "用户 {$fd} 离线\n";
});

$server->start();
```

这样就可以用 `$table->exists($fd)` 来判断用户是否在线。

------

## 5. **常用方法**

| 方法                            | 作用           |
| ------------------------------- | -------------- |
| `column($name, $type, $size)`   | 定义列         |
| `create()`                      | 创建表         |
| `set($key, $value)`             | 设置一行       |
| `get($key, $field = null)`      | 获取一行或某列 |
| `del($key)`                     | 删除一行       |
| `exists($key)`                  | 判断是否存在   |
| `count()`                       | 获取当前行数   |
| `decr($key, $column, $dec = 1)` | 原子递减       |
| `incr($key, $column, $inc = 1)` | 原子递增       |

------

## 6. **注意事项**

1. 表的容量是固定的，不能动态扩容。
2. 创建表必须在 `Server->start()` 之前，否则报错。
3. 字符串列的长度一旦定义就固定，多余部分会被截断。
4. Swoole\Table 是内存结构，进程退出后数据会丢失（除非自己序列化持久化）。

------

✅ **总结**
 `Swoole\Table` 就是一个高速共享内存数据表，用法和 Redis 哈希有点像，但不需要网络开销，适合存储在线用户、排行榜、计数器等多进程共享数据。