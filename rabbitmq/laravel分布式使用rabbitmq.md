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
// 引入 Composer 自动加载文件，加载 RabbitMQ 依赖库
require_once __DIR__ . '/vendor/autoload.php';

// 引入 RabbitMQ 连接类
use PhpAmqpLib\Connection\AMQPStreamConnection;

// ====================== 1. 创建与 RabbitMQ 服务器的连接 ======================
// AMQPStreamConnection 参数说明：
// 1. 主机地址：127.0.0.1（本地）
// 2. 端口号：5672（RabbitMQ 默认端口）
// 3. 用户名：guest（默认管理员账号）
// 4. 密码：guest（默认管理员密码）
$connection = new AMQPStreamConnection('127.0.0.1', 5672, 'guest', 'guest');

// 创建信道（Channel），所有消息操作都基于信道完成
$channel = $connection->channel();

// ========== 关键：开启发布确认模式 ==========
$channel->confirm_select();

// ====================== 2. 声明队列（如果队列不存在则创建） ======================
// queue_declare(队列名, 被动声明, 持久化, 独占队列, 自动删除, 其他参数)
$channel->queue_declare(
    'task_queue',  // 队列名称：task_queue（任务队列）
    false,         // 被动声明：false = 不存在则创建，true = 只检查不创建
    true,          // 持久化：true = 队列会保存到磁盘，RabbitMQ 重启后不丢失
    false,         // 独占队列：false = 允许其他连接访问，true = 仅当前连接可用
    false          // 自动删除：false = 消费者断开后不删除队列
);

// ====================== 3. 设置消费限流（非常重要，防止消费者被压垮） ======================
// basic_qos(预取计数大小, 预取消息数量, 全局设置)
$channel->basic_qos(
    null,  // 预取计数大小：null = 不限制字节，按消息数量限制
    1,     // 预取消息数量：1 = 每次只给消费者发 1 条消息，处理完再发下一条
    null   // 全局设置：null = 只对当前消费者生效
);

// ====================== 4. 定义消息消费回调函数（收到消息后执行） ======================
$callback = function ($msg) {
    // 输出收到的消息内容
    echo "收到消息: ", $msg->body, "\n";

    // 把 JSON 字符串转成 PHP 数组
    $data = json_decode($msg->body, true);

    // 模拟业务处理耗时（比如发送邮件、生成订单、处理数据）
    sleep(2);

    echo "处理完成\n";

    // ====================== 5. 手动确认消息已处理完成 ======================
    // 必须手动 ack，否则 RabbitMQ 会认为消息未处理，重新发给其他消费者
    $msg->ack();
};

// ====================== 6. 注册消费者，开始监听队列消息 ======================
// basic_consume(队列名, 消费者标签, 全局消费, 自动确认, 独占消费, 不等待, 回调函数)
$channel->basic_consume(
    'task_queue', // 监听的队列名称
    '',           // 消费者标签：留空自动生成
    false,        // 全局消费：false = 仅当前信道生效
    false,        // 自动确认：false = 关闭自动确认，必须手动 ack（关键）
    false,        // 独占消费：false = 允许多个消费者同时监听
    false,        // 不等待：false = 等待服务器响应
    $callback     // 消息处理回调函数
);

echo "等待消息...\n";

// ====================== 7. 持续阻塞监听，等待消息到来 ======================
// 只要信道还在消费，就一直等待
while ($channel->is_consuming()) {
    $channel->wait(); // 阻塞等待消息
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

##   补充

###   ack是什么

在 RabbitMQ 里，**ack = acknowledgment（确认）**，本质上是 **消费者告诉 RabbitMQ：“这条消息我已经处理完了。”**

可以把它理解成快递签收：

- RabbitMQ = 快递站
- Producer = 寄件人
- Consumer = 收件人
- ack = 签收确认

如果消费者拿到消息后 **没有 ack**：

- RabbitMQ 会认为这条消息可能没处理成功
- 消费者断开连接 / 崩溃后，这条消息通常会被重新投递给其他消费者
- 消息不会从队列真正删除

如果消费者 **发送 ack**：

- RabbitMQ 知道消息处理完成
- 会把消息从队列移除

#### 两种模式

1. 自动 ack（autoAck=true）

```php
// 6. 开始消费（关闭自动确认）
        $channel->basic_consume(
            'task_queue',
            '',
            false,
            true, // 必须 false（手动 ack）  默认是true
            false,
            false,
            $callback
        );
```



这里 `true` 表示：

> RabbitMQ 一把消息发给 consumer，就默认认为处理成功了

优点：

- 简单
- 性能高

缺点：

- 如果 consumer 刚收到消息就挂了，消息可能丢失

2. 手动 ack（autoAck=false）

```php
       // 6. 开始消费（关闭自动确认）
        $channel->basic_consume(
            'task_queue',
            '',
            false,
            false, // 必须 false（手动 ack）  默认是true
            false,
            false,
            $callback
        );
```

然后处理完成后手动确认：

```php

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
```

#### 为什么要手动 ack？

因为自动 ack 可能导致消息丢失：

1. RabbitMQ 发消息
2. Consumer 还没处理完
3. Consumer 挂了
4. RabbitMQ 已经认为消息完成 → 消息丢失

手动 ack 可以保证：

> 至少被消费一次（At-least-once）