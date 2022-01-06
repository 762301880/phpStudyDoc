# tcp服务端

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
apt -y install telnet
telnet 127.0.0.1 9501
```

# [tcp客户端](https://wiki.swoole.com/wiki/page/p-client.html)

> 建立**client.php**

```php

```



# 实战演练

## 计算在线人数

```php
# 用户连接事件 用户连接的时候发送一个连接通知
use Swoole\WebSocket\Server;
use Swoole\Http\Request;
$ws->on('open', function (Server $server, Request $request) {
    $server->push($request->fd,['msg'=>'当前在线人数','num'=>count($server->connections)]);
});
```

