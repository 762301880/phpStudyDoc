## GatewayWorker资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| GatewayWorker官方文档 | [link](https://www.workerman.net/doc/gateway-worker/README.html) |
| 博客pdf               | [pdf](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/ThinkPHP%205.1%20Workerman%20%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20ThinkPHP5.1%20Workerman%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20%E7%9C%8B%E4%BA%91.pdf) |

## [tp下安装GatewayWorker](https://www.kancloud.cn/manual/thinkphp5_1/354134)

### 1. 直接安装封装好的扩展库

**安装服务端**

> composer安装

```php
composer require topthink/think-worker=2.0.*
```

**对应的配置文件解析**

> 这三个配置文件都属于 GatewayWorker 扩展包，用于实现 WebSocket 和 TCP 长连接服务的配置。
>
> - `gateway_worker.php` 是 GatewayWorker 服务的核心配置文件，它定义了服务的各种参数，如监听的 IP 和端口、进程数、Worker 和 Gateway 的运行模式、协议类型等。
> - `worker.php` 是 Worker 进程的配置文件，用于设置 Worker 进程相关的参数，例如进程的名称、任务处理逻辑、定时任务、各种事件的处理等。
> - `worker_server.php` 是 WorkerServer 进程的配置文件，通常用于设置服务器的一些基本参数，例如监听的 IP 和端口、WorkerServer 的运行模式等。

这三个配置文件中定义了不同层次的配置参数，通过它们，我们可以很方便地对 GatewayWorker 的各项功能和性能进行配置，并启动和管理相关的进程。





## bug解析

### 多进程情况下定时任务或者crontab定时任务执行多次的问题

> 问题:假如我在onWorkerStart中执行定时任务或者workerman/crontab 但是开启多个进程之后里面的任务会执行多次
>
> 在workerman中，onWorkerStart方法是在子进程启动时触发的，当启动多个子进程后，每个子进程都会执行一遍onWorkerStart方法，导致定时任务可能被重复执行。
>
> 解决这个问题的方法是，使用workerman/crontab组件的单例模式，确保所有的子进程共享一个计划任务列表。具体做法如下：

第一步，在onWorkerStart方法中初始化计划任务列表，并将其保存到一个全局变量中：

```php
use Workerman\Worker;
use Workerman\Crontab;

$worker = new Worker();
$worker->count = 4;
$worker->onWorkerStart = function($worker)
{
    global $crontab;
    // 初始化crontab实例
    $crontab = new Crontab();
    // 添加计划任务
    $crontab->addTask('* * * * *', function(){
        // 需要执行的任务
    });
};
Worker::runAll();
```



第二步，在定时任务的回调函数中添加进程编号的判断，只在主进程执行该任务，避免重复执行：

```php
use Workerman\Worker;
use Workerman\Crontab;

$worker = new Worker();
$worker->count = 4;
$worker->onWorkerStart = function($worker)
{
    global $crontab;
    // 初始化crontab实例
    $crontab = new Crontab();
    // 添加计划任务
    $crontab->addTask('* * * * *', function() use ($worker){
        // 判断是否在主进程中执行
        if($worker->id === 0) {
            // 需要执行的任务
        }
    });
};
Worker::runAll();
```

> 我是在 topthink/think-worker 扩展包 的worker_server.php中的onWorkerStart回调中写的怎么处理呢

```php
'onWorkerStart' => function ($worker) {
        if ($worker->id === 0) {
            \Workerman\Lib\Timer::add(10, function () {
                \think\facade\Log::info('555555555555555' . date('Y-m-d H:i:s'));
            });
            new \Workerman\Crontab\Crontab('1 * * * * *', function () {
                \think\facade\Log::info("aaaaaaaaaaaaaaaaaaa" . date('Y-m-d H:i:s'));
            });
        }
    },
```



##  问题

### websocket聊天如何发送图片给后端

在workerman websocket中发送图片给后端可以采取多种方式，以下是其中两种常见的方法：

#### 将图片数据作为二进制流发送

前端将图片数据读取为二进制流后，通过websocket发送给后端，后端再将二进制流保存成图片文件。前端(客户端)发送代码示例：

```php
const reader = new FileReader();
reader.onload = e => {
  const blob = new Blob([e.target.result]);
  // 通过websocket发送blob二进制流
  ws.send(blob);
}

const file = document.querySelector('input[type=file]').files[0];
reader.readAsArrayBuffer(file);
```

在后端接收websocket消息的回调函数中，可以使用`file_put_contents`等函数将二进制流转化为图片保存到服务器上：

```php
use Workerman\Protocols\Http\Request;

$worker->onMessage = function($connection, $data) {
    
    // 文件保存目录
    $uploadPath = __DIR__ . '/uploads/';
    
    // 将二进制数据保存到文件
    file_put_contents($uploadPath . uniqid() . '.jpg', $data);
};
```

**如何判断传输过来的数据是否是二进制**

> 在 PHP 中，我们可以使用 `is_numeric()` 函数来判断一个字符串是否为数字，但是如果要检查参数是否为二进制，则需要使用另外一个函数，即 `ctype_print()`。`ctype_print()` 函数检查指定的字符串中是否包含可打印字符。如果包含不可打印字符（二进制），则返回 `false`。如果不包含不可打印字符，则返回 `true`。
>
> 例如，下面的代码检查 `$data` 是否为二进制字符串：
>
> 需要注意的是，为了避免误判，`ctype_print()` 函数只能作为判断字符串是否包含二进制字符的一种基本方法。如果需要更加精确的判断，可以考虑使用其他方法，比如查看字符串中是否包含 ASCII 控制字符或非 ASCII 字符等。

```php
if (ctype_print($data)) {
    // $data 中不包含二进制字符，可以当作文本字符串处理
} else {
    // $data 中包含二进制字符，需要特殊处理
}
```



#### 将图片转化为base64编码后发送

前端将图片数据转化为base64编码后，通过websocket发送给后端，后端再将base64编码的字符串转化为图片文件。前端(客户端)发送代码示例：

```php
const reader = new FileReader();
reader.onload = e => {
  const base64Image = e.target.result.split(',')[1];
  // 通过websocket发送base64编码后的图片数据
  ws.send(base64Image);
}

const file = document.querySelector('input[type=file]').files[0];
reader.readAsDataURL(file);
```

在后端接收websocket消息的回调函数中，可以使用`base64_decode`等函数将base64编码的图片数据转化为图片文件保存到服务器上：

```php
use Workerman\Protocols\Http\Request;

$worker->onMessage = function($connection, $data) {
    
    // 文件保存目录
    $uploadPath = __DIR__ . '/uploads/';
    
    // 将base64编码的字符串转化为图片
    $imgData = base64_decode($data);
    // 将图片保存到文件
    file_put_contents($uploadPath . uniqid() . '.jpg', $imgData);
};
```

**判断传输过来的数据是否是base64编码**

> 在 PHP 中，可以使用 `base64_decode()` 函数来尝试将接收到的数据尝试解码为 Base64 字符串。如果解码成功，则说明接收到的数据是一个可解码的 Base64 字符串，否则说明数据不是一个合法的 Base64 编码的字符串。可以按照以下方式来进行判断
>
> 需要注意的是，如果接收到的数据不是一个合法的 Base64 编码的字符串，`base64_decode()` 函数会返回 `false`，而不是抛出异常。因此，在进行数据判断之前我们需要先检查解码结果是否合法。另外，我们还需要添加异常处理机制来防止非法数据引发异常。

```php
$data = '...'; // 传输过来的数据

// 使用 try-catch 来防止非法数据引发异常
try {
    // 尝试将数据解码为 Base64 字符串
    $decodedData = base64_decode($data, true);
    
    // 判断是否是合法的 Base64 字符串
    if (base64_encode($decodedData) === $data) {
        // 数据是一个合法的 Base64 编码的字符串
    } else {
        // 数据不是一个合法的 Base64 编码的字符串
    }
} catch (Exception $e) {
    // 解码失败，数据不是一个合法的 Base64 编码的字符串
}
```





##  帮助

###  [测试websocket连接](http://coolaf.com/tool/chattest)

> 使用websocket在线测试工具测试连接

![image-20240305113625977](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20240305113625977.png)