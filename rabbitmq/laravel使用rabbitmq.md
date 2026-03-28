## 注意这种不属于分布式调用,只支持laravel调用rabbitmq

## 安装第三方扩展包

```php
composer require vladimir-yuldashev/laravel-queue-rabbitmq
```

## 配置 queue.php

编辑 `config/queue.php`：

```php
'connections' => [

    'rabbitmq' => [
        'driver' => 'rabbitmq',

        'hosts' => [
            [
                'host' => env('RABBITMQ_HOST', '127.0.0.1'),
                'port' => env('RABBITMQ_PORT', 5672),
                'user' => env('RABBITMQ_USER', 'guest'),
                'password' => env('RABBITMQ_PASSWORD', 'guest'),
                'vhost' => env('RABBITMQ_VHOST', '/'),
            ],
        ],

        'queue' => env('RABBITMQ_QUEUE', 'default'),
        'exchange' => env('RABBITMQ_EXCHANGE_NAME'),
        'exchange_type' => 'direct',
    ],

],
```

配置 .env

```php
QUEUE_CONNECTION=rabbitmq

RABBITMQ_HOST=127.0.0.1
RABBITMQ_PORT=5672
RABBITMQ_USER=guest
RABBITMQ_PASSWORD=guest
RABBITMQ_QUEUE=default
```

## 创建队列任务（Job）

```php
php artisan make:job TestRabbitMQ
```

### 编辑 Job：

```php
class TestRabbitMQ implements ShouldQueue
{
    use Dispatchable, InteractsWithQueue, Queueable, SerializesModels;

    public function handle()
    {
        \Log::info('RabbitMQ job executed');
    }
}
```

## 发送任务到 RabbitMQ

```php
TestRabbitMQ::dispatch();
```

## 启动队列消费者

```php
php artisan queue:work rabbitmq
    
php artisan queue:listen rabbitmq    
```

## 进阶（常用配置）

```php
# 指定队列
TestRabbitMQ::dispatch()->onQueue('emails');

# 延迟队列
TestRabbitMQ::dispatch()->delay(now()->addSeconds(10));

# 多消费者（高并发）

php artisan queue:work rabbitmq --queue=default --tries=3
```

