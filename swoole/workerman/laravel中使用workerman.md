

#   一、资料&安装

## 1.1、资料

| 名称            | 地址                              |
| --------------- | --------------------------------- |
| `workerman`文档 | [地址](http://doc.workerman.net/) |
|                 |                                   |

## 1.2安装

- 使用composer安装

```php
composer require workerman/workerman
```

- 使用`github`安装

```php
git clone https://github.com/walkor/Workerman
```

- [更多安装说明请查阅官方稳定](http://doc.workerman.net/install/install.html)

# 二、`laravel`中使用`workerman`示例

## 2.1在`laravel`中创建一个命令类用于启用`workerman`

- 使用artisan创建命令

```shell
php artisan make:command Workerman
```

- 在`app\Console\Commands\Workerman.php`中修改

> 修改`protected $signature = '自定义命令';`
>
> 与 handle()方法

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Workerman\Worker;

class Workerman extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    # 定义启动命令
    protected $signature = 'Workerman:command {action} {-d}';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Workerman Server';

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
       // 创建一个Worker监听9011端口，使用websocket协议通讯
        $websocket_worker  = new Worker("websocket://0.0.0.0:9011");
        // 启动4个进程对外提供服务
        $websocket_worker ->count = 4;
        //用户连接时候触发
        $websocket_worker->onConnect = function($connection)
        {
             $connection->send('欢迎您主人');
        };
        // 接收到浏览器发送的数据时回复信息给浏览器
        $websocket_worker ->onMessage = function ($connection, $data) {
          // 向浏览器发送欢迎信息 $data客户端发送来的信息
            $connection->send('您好,您发送了：' . $data);
        };
        //用户关闭时候触发
        $websocket_worker->onClose = function($connection)
        {
            echo '再见';
        };
        // 运行worker
        Worker::runAll();
    }
}

```

## 2.2在项目中使用命令启用`workerman`

### 2.2.1 使用命令启用

```shell
php artisan workerman:command start  d
```

### 2.2.2启用之后控制台示例

```php
----------------------- WORKERMAN -----------------------------
Workerman version:4.0.19          PHP version:7.3.4
------------------------ WORKERS -------------------------------
worker               listen                              processes status
none                 websocket://0.0.0.0:9011            4         [ok]
```

## 2.3 workerman简单实现进入事件的功能

```php
  // 创建一个Worker监听9011端口，使用websocket协议通讯
        $websocket_worker  = new Worker("websocket://0.0.0.0:9011");
        // 启动4个进程对外提供服务
        $websocket_worker ->count = 4;
        $uid=0;//默认定义用户id初始化为0
        //用户连接时候触发
        $websocket_worker->onConnect = function($connection)use ($uid,$websocket_worker)
        {
             $connection->uid=++$uid;
            foreach ($websocket_worker->connections as $conn){
                $conn->send("user_{$connection->uid} 进来了" );
            }
        };
        // 接收到浏览器发送的数据时回复信息给浏览器
        $websocket_worker ->onMessage = function ($connection, $data)use ($websocket_worker) {

            foreach ($websocket_worker->connections as $conn){
                $conn->send("user_{$connection->uid} 说了" . $data);
            }

        };
        //用户关闭时候触发
        $websocket_worker->onClose = function($connection)use ($websocket_worker)
        {
            foreach ($websocket_worker->connections as $conn){
                $conn->send("user_{$connection->uid}走了");
            }
        };
        // 运行worker
        Worker::runAll();
```

