# 一、说明

> 例如我们需要注册的时候需要发送一条邮件提醒用户，平时的时候我们直接
> 在逻辑层注册完成之后执行一个发送邮件，这样做的话我们必须要等待邮件发送
> 完毕的时候用户才可以注册成功，效率很慢，这个时候我们就需要用到队列的功能了

## 1.1 参考资料

| 项目名称         | 项目地址                                                     |
| ---------------- | ------------------------------------------------------------ |
| laravel-队列     | [链接](https://learnku.com/docs/laravel/8.x/queues/9398#introduction) |
| 消息队列实战教程 | [链接](https://laravelacademy.org/books/laravel-queue-action) |

# 二、配置

## 2.1 配置驱动

>1. 注意在使用的时候最好不要在 `.env`中配置请用# 注释#QUEUE_CONNECTION 
>
>2. 在`config\queue.php`中配置更直观
>
>   ```php
>   'default' => env('QUEUE_CONNECTION', 'database'),
>   ```
>
>3. 在`.env`文件中配置QUEUE_CONNECTION=database
>     `Laravel`可配置多种队列驱动，包括 "sync", "database", "beanstalkd", "sqs", "redis", "null"（具体参见app/config/queue.php）
>     其中sync为同步，database为使用数据库，后面三种为第三方队列服务，最后一种为不使用队列。
>     通过在 .env 中的 QUEUE_CONNECTION 选项，来决定选择何种驱动。
>     如 QUEUE_CONNECTION=database 即为选择数据库驱动队列。

## 2.2 生成`mysql`队列表

> 1. 为了方便演示我们在mysql中使用队列
> 2. 你需要一张数据表来存储任务。
>    运行 queue:table Artisan 命令来创建这张表的迁移文件。
>    当迁移文件创建好后，你就可以使用 migrate 命令来进行迁移

```php
#生成迁移文件
php artisan queue:table
#生成迁移文件
php artisan migrate
```

- 生成的数据表

```sql
# 以上命令会在mysql中生成 1.jobs 2.failed_jobs 表这里我们主要看一下 jobs表
CREATE TABLE `jobs` (
  `id` bigint(20) unsigned NOT NULL AUTO_INCREMENT,
  `queue` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `payload` longtext COLLATE utf8mb4_unicode_ci NOT NULL,
  `attempts` tinyint(3) unsigned NOT NULL,
  `reserved_at` int(10) unsigned DEFAULT NULL,
  `available_at` int(10) unsigned NOT NULL,
  `created_at` int(10) unsigned NOT NULL,
  PRIMARY KEY (`id`),
  KEY `jobs_queue_index` (`queue`)
) ENGINE=InnoDB AUTO_INCREMENT=5 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci; 
```

# 三、使用队列

## 3.1  创建`队列任务`

```php
#使用artisan命令生成
php artisan  make:job SaveLog
#该命令会在app\Jobs\SaveLog.php中
```

- `SaveLog`示例代码

```php
  public $message;
  #构建参数
  public function __construct($message)
    {
        $this->message=$message;
    }
  # 执行工作：编写逻辑的位置
  public function handle()
    {
       Log::info('队列实验'.$this->message); # 这里我们采用保存日志的方式实验
    }  
```

## 3.2调用队列

- 更多调用队列的方式

```php
$this->dispatch(new SaveLog('您好')); 
/* 1. 延迟分发
 * 如果你希望有条件地执行队列任务，可以在分发任务时使用 delay 方法 。例如，让我们指定调度任务在 10 分钟后他被调度后才执行，在这之前它将是无效的：
 */
SaveLog::dispatch('你好啊,这是一次队列实验')->delay(now()->addMinutes(10));
/*
 * 2. 队列优先级
 * 有时，你可能希望优先考虑如何处理队列。例如，在 config/queue.php 中，
 * 你可以将你的 redis 连接的默认 queue 设置为 low。然而，有时你可能希望将一个任务推   到一个 high 优先级队列，就像这样:
 */
dispatch((new Job)->onQueue('high'));
```

```php
# 在控制器中调用队列
use App\Jobs\SaveLog;  
public function test(Request $request)
  {
    SaveLog::dispatch('你好啊,这是一次队列实验');# 调用队列 传入参数你好啊,这是一次队列实验
    return;
  }
```

> 这个时候应为队列没有启用开启执行队列，数据库的队列表中会有一个任务，等待队列启动后执行，所以可以在数据库中的`jobs`表中看见没有执行的数据

## 3.3 执行命令开启队列

```php
php artisan queue:work
#  php artisan queue:work --queue=high,low  加上参数设置队列的优先级 d
```

> 执行以上命令就会监听数据库是否有未执行的队列如果有则消费队列你就会在`storage\logs\laravel.log`中看见对应的数据

## 3.4  linux Supervisor 配置队列守护进程

### 安装 [Supervisor](https://learnku.com/docs/laravel/8.x/queues/9398#38f783)

> Supervisor 是一个用于 Linux 操作系统的进程监视器，如果 `queue:work` 进程失败，它将自动重启该进程。要在 Ubuntu 上安装 Supervisor，你可以使用以下命令：

```shell
sudo apt-get install supervisor
```

#### Supervisor [配置](https://learnku.com/docs/laravel/8.x/queues/9398#e45763)

>Supervisor 配置文件通常存储在 /etc/supervisor/conf.d 目录。在此目录中，你可以创建任意数量的配置文件，这些配置文件将指示 supervisor 如何监视你的进程。例如，让我们创建一个 laravel-worker.conf 文件，启动并监视 queue:work 进程：

```shell
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3
autostart=true
autorestart=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
stopwaitsecs=3600
```

  >在本例中， numprocs 指令将指示监控器运行 8 个 queue:work 进程并监视所有进程，如果它们失败，将自动重新启动它们。你应该更改 command 指令的 queue:work sqs 部分，以反映所需的队列连接。
  >
  >注意：应该确保 `stopwaitsecs` 的值大于运行时间最长的任务所消耗的秒数。否则，Supervisor 可能会在任务完成前终止任务。

#### 启动 Supervisor

>创建了配置文件后，你可以使用以下命令更新 Supervisor 配置并启动进程：

```shell
sudo supervisorctl reread

sudo supervisorctl update

sudo supervisorctl start laravel-worker:*
```

有关 Supervisor 的更多信息，请参考 [Supervisor documentation](http://supervisord.org/index.html)

## 3.5更多使用请查阅官方[文档](https://learnku.com/docs/laravel/8.x/queues/9398#e43937)

