##  php-amqplib

```php
composer require php-amqplib/php-amqplib
```

##  案例

### 生产者（发送消息）

```php
<?php
require_once __DIR__ . '/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

// 1. 连接 RabbitMQ
$connection = new AMQPStreamConnection(
    '127.0.0.1', // RabbitMQ地址
    5672,        // 端口
    'guest',     // 用户名
    'guest'      // 密码
);

$channel = $connection->channel();

// 2. 声明队列（不存在会自动创建）
$channel->queue_declare(
    'task_queue', // 队列名称
    false,
    true,  // 持久化队列（很重要！）
    false,
    false
);

// 3. 要发送的数据
$data = [
    'order_id' => 123,
    'msg' => '创建订单'
];

// 4. 创建消息（设置持久化）
$msg = new AMQPMessage(
    json_encode($data),
    [
        'delivery_mode' => AMQPMessage::DELIVERY_MODE_PERSISTENT // 持久化消息
    ]
);

// 5. 发送消息
$channel->basic_publish($msg, '', 'task_queue');

echo "消息发送成功\n";

// 6. 关闭
$channel->close();
$connection->close();
```

###  （发送消息）

### 消费者（处理消息）

```php
<?php
require_once __DIR__ . '/vendor/autoload.php';

use PhpAmqpLib\Connection\AMQPStreamConnection;

// 1. 连接
$connection = new AMQPStreamConnection('127.0.0.1', 5672, 'guest', 'guest');
$channel = $connection->channel();

// 2. 声明队列
$channel->queue_declare('task_queue', false, true, false, false);

// 3. 限流（避免一个消费者被压死）
$channel->basic_qos(null, 1, null);

// 4. 消费逻辑
$callback = function ($msg) {
    echo "收到消息: ", $msg->body, "\n";

    $data = json_decode($msg->body, true);

    // 模拟业务处理
    sleep(2);

    echo "处理完成\n";

    // 5. 手动确认（非常重要！）
    $msg->ack();
};

// 6. 开始消费（关闭自动确认）
$channel->basic_consume(
    'task_queue',
    '',
    false,
    false, // 必须 false（手动 ack）
    false,
    false,
    $callback
);

echo "等待消息...\n";

// 持续监听
while ($channel->is_consuming()) {
    $channel->wait();
}
```

##  Laravel 中使用（真实项目写法）

###  封装一个 RabbitMQ 类

`app/Services/RabbitMQService.php`

```php
<?php

namespace App\Services;

use PhpAmqpLib\Connection\AMQPStreamConnection;
use PhpAmqpLib\Message\AMQPMessage;

class RabbitMQService
{
    protected $connection;
    protected $channel;

    public function __construct()
    {
        $this->connection = new AMQPStreamConnection(
            env('RABBITMQ_HOST'),
            env('RABBITMQ_PORT'),
            env('RABBITMQ_USER'),
            env('RABBITMQ_PASS')
        );

        $this->channel = $this->connection->channel();
    }

    // 发送消息
    public function publish($queue, $data)
    {
        $this->channel->queue_declare($queue, false, true, false, false);

        $msg = new AMQPMessage(
            json_encode($data),
            ['delivery_mode' => AMQPMessage::DELIVERY_MODE_PERSISTENT]
        );

        $this->channel->basic_publish($msg, '', $queue);
    }

    // 消费消息
    public function consume($queue, $callback)
    {
        $this->channel->queue_declare($queue, false, true, false, false);

        $this->channel->basic_qos(null, 1, null);

        $this->channel->basic_consume($queue, '', false, false, false, false, $callback);

        while ($this->channel->is_consuming()) {
            $this->channel->wait();
        }
    }
}
```

### Controller 发送消息

```php
public function send(RabbitMQService $mq)
{
    $mq->publish('task_queue', [
        'user_id' => 1,
        'action' => '注册'
    ]);

    return '发送成功';
}
```

### Artisan 命令（消费者）

```php
php artisan make:command RabbitConsumer
```

```php
public function handle()
{
    $mq = new \App\Services\RabbitMQService();

    $mq->consume('task_queue', function ($msg) {
        echo $msg->body . PHP_EOL;

        // 业务逻辑
        sleep(1);

        $msg->ack();
    });
}
```

运行：

```php
php artisan RabbitConsumer
```

## ThinkPHP 用法（核心一样）

本质：**和原生 PHP 一模一样，只是换个位置写**

```php
$mq = new RabbitMQService();

$mq->publish('task_queue', ['id' => 1]);
```

消费者建议写成：

```php
php think queue:consume
```

