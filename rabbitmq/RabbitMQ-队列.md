#  说明

## [概述](https://baike.baidu.com/item/rabbitmq/9372144?fr=aladdin)

> RabbitMQ 是实现高级消息队列协议 (AMQP) 的开源消息代理软件（有时称为面向消息的中间件）。
>
> RabbitMQ 服务器是用 Erlang 编程语言编写的，并建立在 Open Telecom Platform 框架上，用于集群和故障转移。
>
> 与代理交互的客户端库可用于所有主要编程语言。

# 资料

| 名称             | 地址                                       |
| ---------------- | ------------------------------------------ |
| 第三方博客       | [link](https://learnku.com/articles/43080) |
| rabbitmq-官网    | [link](https://www.rabbitmq.com/)          |
| rabbitmq中文文档 | [link](https://rabbitmq.org.cn/docs)       |

# Rabbitmq基本概念

RabbitMQ核心就三样：

- **Producer（生产者）**：发消息
- **Queue（队列）**：存消息
- **Consumer（消费者）**：处理消息

中间还有个关键东西：

- **Exchange（交换机）**：决定消息发到哪个队列

# 安装

## docker安装RabbitMQ

**资料**

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| docker-rabbitmq 官方镜像 | [link](https://hub.docker.com/_/rabbitmq)  [兔子 - 官方图片 \|码头工人中心 (docker.com)](https://registry.hub.docker.com/_/rabbitmq/) |

**安装**

> 安装后默认用户名&密码为**guest**

```shell
# 请用下面的示例安装-这里只是展示基础安装方法
docker run -itd --rm --name rabbitmq -p 5672:5672 -p 15672:15672 rabbitmq:3.12-management
```

**设置默认用户和密码**

> 如果要更改 / 的默认用户名和密码，可以使用 和 环境变量执行此操作。这些变量以前在特定于 docker 的入口点 shell 脚本中可用，但现在可以直接在 RabbitMQ 中使用。`guest``guest``RABBITMQ_DEFAULT_USER``RABBITMQ_DEFAULT_PASS`

```shell
docker run -itd \
--hostname my-rabbit \
--name some-rabbit \
-p 5672:5672 \
-p 15672:15672 \
-e RABBITMQ_DEFAULT_USER=user \
-e RABBITMQ_DEFAULT_PASS=password \
rabbitmq:3.12-management


# 例子
docker run -itd \
--hostname my-rabbit \
--name some-rabbit \
-p 5672:5672 \
-p 15672:15672 \
-e RABBITMQ_DEFAULT_USER=yaoliuyang \
-e RABBITMQ_DEFAULT_PASS=yaoliuyang \
rabbitmq:3.12-management
```



# 实战

## laravel

### php-amqplib/php-amqplib 扩展包

**资料**

| 名称                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| 第三方扩展包-php-amqplib/php-amqplib | [扩展包地址](https://packagist.org/packages/php-amqplib/php-amqplib)  [文档地址](http://php-amqplib.github.io/php-amqplib/namespaces/phpamqplib.html) |
| 相关使用文档(推荐这个)               | [RabbitMQ Tutorials — RabbitMQ](https://www.rabbitmq.com/getstarted.html)                          [RabbitMQ教程 - “你好世界！ — 兔子MQ](https://www.rabbitmq.com/tutorials/tutorial-one-php.html) |

###  RabbitMQ单例模式封装

```php
<?php

namespace App\Services;

use PhpAmqpLib\Connection\AMQPStreamConnection;

class RabbitMQService
{
    private static $instance = null;

    private $connection = null;
    private $channel = null;

    /**
     * 防止创建实例
     */
    private function __construct() {}

    /**
     * 获取单例
     */
    public static function getInstance(): self
    {
        if (self::$instance === null) {
            self::$instance = new self();
        }

        return self::$instance;
    }

    /**
     * 获取连接（懒加载）
     */
    public function getConnection(): AMQPStreamConnection
    {
        if (!$this->connection || !$this->connection->isConnected()) {

            $this->connection = new AMQPStreamConnection(
                config('rabbitmq.host', '47.107.33.56'),
                config('rabbitmq.port', 5672),
                config('rabbitmq.username', 'yaoliuyang'),
                config('rabbitmq.password', 'yaoliuyang'),
                config('rabbitmq.vhost', '/')
            );

            // 每次重连，channel 也要重建
            $this->channel = null;
        }

        return $this->connection;
    }

    /**
     * 获取 channel
     */
    public function getChannel()
    {
        if (!$this->channel) {
            $this->channel = $this->getConnection()->channel();
        }

        return $this->channel;
    }

    /**
     * 手动关闭（可选）
     */
    public function close()
    {
        if ($this->channel) {
            $this->channel->close();
            $this->channel = null;
        }

        if ($this->connection) {
            $this->connection->close();
            $this->connection = null;
        }
    }
}
```



### **简单实验**

#### 订阅

```php
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use PhpAmqpLib\Connection\AMQPStreamConnection;

class MqSub extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'mq_sub';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'test rabbitmq sub';

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
        $exchange = 'Gaming';
        $routerKey = 'lol'; //只订阅LOL消息

//        $connection = new AMQPStreamConnection('60.204.148.255', 5672, 'guest', 'guest');
//        $channel = $connection->channel();

        $connection = RabbitMQService::getInstance();
        $channel = $connection->getChannel();
        
        $channel->exchange_declare($exchange, 'direct', false, false, false);
        list($queueName, ,) = $channel->queue_declare("", false, false, true, false);
        $channel->queue_bind($queueName, $exchange, $routerKey);

        echo " 等待消息中..." .PHP_EOL;
        $callback = function ($msg) {
            echo '接收到消息：',$msg->delivery_info['routing_key'], ':', $msg->body, PHP_EOL;
            sleep(1);  //模拟耗时执行
        };
        $channel->basic_consume($queueName, '', false, true, false, false, $callback);

        while ($channel->is_consuming()) {
            $channel->wait();
        }

        $channel->close();
        $connection->close();

    }
}
```

#### 发布

```php
public function publish(Request $request)
    {
        $exchange = 'Gaming';
//        $connection = new AMQPStreamConnection('60.204.148.255', 5672, 'guest', 'guest');
//        $channel = $connection->channel();
    
        $connection =RabbitMQService::getInstance();
        $channel = $connection->getChannel();
        $channel->exchange_declare($exchange, 'direct', false, false, false);
        for ($i = 0; $i < 100; $i++) {
            $routes = ['dota', 'csgo', 'lol'];
            $key = array_rand($routes);
            $arr = [
                'match_id' => $i,
                'status' => rand(0, 3)
            ];
            $data = json_encode($arr);
            $msg = new AMQPMessage($data);

            $channel->basic_publish($msg, $exchange, $routes[$key]);
            echo '发送 ' . $routes[$key] . ' 消息: ' . $data . PHP_EOL;
        }
        $channel->close();
        $connection->close();

    }
```



## RabbitMQ 常见坑（重点！！！）

###   坑1：消息丢失

#### 原因

- 队列没持久化
- 消息没持久化
- 没 ack

#### 解决（必须三件套）

```php
// 队列持久化
$channel->queue_declare('task_queue', false, true, false, false);

// 消息持久化
new AMQPMessage($data, ['delivery_mode' => 2]);

// 手动 ack
$msg->ack();
```

### 坑2：消费者挂了消息丢了

```php
# 错误写法
basic_consume(..., true, ...) // 自动确认 ❌
    
# 正确
basic_consume(..., false, ...) // 手动确认 ✅    
```

### 坑3：消息重复消费（幂等性问题）

**解决方案：加唯一ID**

```php
$data = [
    'order_id' => 123
];
```

数据库加唯一索引：

```php
UNIQUE(order_id)
```

或者 Redis：

```php
if (Redis::setnx("order:{$id}", 1)) {
    Redis::expire("order:{$id}", 60);
    // 执行业务
}
```

###  坑4：消费者卡死

解决：限流

```php
$channel->basic_qos(null, 1, null);
```

### 坑5：任务失败怎么办？

解决：NACK + 重入队列

```php
try {
    // 业务
    $msg->ack();
} catch (\Exception $e) {
    // 重新入队
    $msg->nack(true);
}
```

### 坑6：死信队列（非常重要）

### 场景

任务失败多次 → 不要无限重试

```php
$args = new \PhpAmqpLib\Wire\AMQPTable([
    'x-dead-letter-exchange' => 'dlx_exchange'
]);

$channel->queue_declare('task_queue', false, true, false, false, false, $args);
```

### 坑7：连接断开

解决：自动重连

```php
while (true) {
    try {
        // 消费逻辑
    } catch (\Exception $e) {
        sleep(5);
    }
}
```

