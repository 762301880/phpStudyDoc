## 项目中如何调试swoole

> ### Swoole 禁止在回调函数里直接用 `echo`、`var_dump` 输出（会乱码 / 不显示），**必须用日志**。

### 使用echo 输出(不推荐会乱码)

> 例如你在swoole中使用**echo** 进行调试语句输出, **cli**启动swoole 后就可以在控制台看到输出的调试语句
>
> **假如你是supervisor中启动  你就要在supervisor配置的日志文件中查看输出** 

```php
    public function WorkerStart($server, $worker_id)
    {
        echo('WorkerStart' . $worker_id).PHP_EOL;//cli启动swoole调试使用
     }

```

### 自带日志（推荐）

```php
// 开启 Swoole 日志
$server = new Swoole\Http\Server('0.0.0.0', 9501);
$server->set([
    'log_file' => '/tmp/swoole.log', // 日志文件
    'log_level' => SWOOLE_LOG_DEBUG, // 日志级别
]);

// 调试打印
$server->on('Request', function ($req, $res) {
    // 打印变量（数组/对象都能输出）
    swoole_dump($req);
    // 普通日志
    echo "请求到达\n"; 
});
```

### 项目通用日志函数

```php
function debug($msg, $data = null) {
    $str = date('Y-m-d H:i:s') . ' ' . $msg;
    if ($data) $str .= ' ' . var_export($data, true);
    file_put_contents('/tmp/debug.log', $str . PHP_EOL, FILE_APPEND);
}

// 使用
debug('接收参数', $_GET);
```

**实时查看日志命令：**

```php
tail -f /tmp/swoole.log
tail -f /tmp/debug.log
```

##  原生swoole如何热重启

### 热重启命令

先找到 Swoole 主进程 PID

```bash
ps aux | grep swoole
```

你会看到类似：

```bash
root      1234  0.0  0.2 123456  7890 ?        Ss   10:00   0:00 php server.php
```

**1234 就是主进程 PID**

### 执行热重启（只杀 worker，不杀主进程）

```bash
kill -USR1 主进程PID

# 例子：
kill -USR1 1234
```

✅ 执行后：

- 服务**不中断**
- 连接**不断开**
- 新代码**立即生效**

### 最简单：一键重启脚本（推荐）

每次找 PID 太麻烦，直接写个 `reload.sh`：

```bash
#!/bin/bash
pid=$(ps aux | grep swoole | grep -v grep | grep master | awk '{print $2}')
kill -USR1 $pid
echo "热重启成功，PID：$pid"
```

加权限：

```bash
chmod +x reload.sh
```

以后想重启，直接执行：

```bash
./reload.sh
```

### 必须知道的 3 个关键点

1. **热重启只重启 worker 进程，不重启 master 主进程**
2. **修改了 server->set () 配置、新增端口、新增服务监听 → 必须冷重启（停止再启动）**
3. 只有业务代码（控制器、路由、逻辑）才能热更新生效