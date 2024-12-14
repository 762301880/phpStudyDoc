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

#  补充

##  swoole 线程 协程 进程之间的关系

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