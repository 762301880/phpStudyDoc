# 说明



laravel-学院[redis实战参考](https://laravelacademy.org/books/high-performance-redis)

有空待研究[redis-json](https://oss.redis.com/redisjson/)

[云redis数据库](https://app.redislabs.com/#/subscriptions/subscription/1703229/bdb)



# [消息订阅与发布](https://learnku.com/docs/laravel/8.x/redis/9405#08edd9)

**创建订阅**

```php
# php artisan make:command RedisSubscribe
    
<?php

namespace App\Console\Commands;

use Illuminate\Console\Command;
use Illuminate\Support\Facades\Redis;

class RedisSubscribe extends Command
{
    /**
     * The name and signature of the console command.
     *
     * @var string
     */
    protected $signature = 'redis:subscribe';

    /**
     * The console command description.
     *
     * @var string
     */
    protected $description = 'Subscribe to a Redis channel';

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
        $redis = Redis::connection();
        $redis->subscribe(['test-channel'], function ($message) {
             echo $message;
        });
    }
}    
```

**测试订阅类**

```php
 public function test(Request $request)
    {
        $redis=Redis::connection()->client();
        $redis->publish('test-channel', json_encode(['foo' => 'bar']));
    }
```

