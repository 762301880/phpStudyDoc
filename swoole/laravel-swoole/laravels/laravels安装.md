# 说明&资料

## 说明

> `LaravelS`是胶水，用于快速集成`Swoole`到`Laravel`或`Lumen`，然后赋予它们更好的性能、更多可能性
>
> 总体来说laravels是一个集成到laravel中的swoole框架

## 资料

| name                           | url                                               |
| ------------------------------ | ------------------------------------------------- |
| gitee-laravels文档             | [link](https://gitee.com/zml956/laravel-s)        |
| laravel学院-swoole从入门到实战 | [link](https://laravelacademy.org/post/9801.html) |

# 安装&使用

## 安装

- 使用***composer***安装**laravelS**

```shell
composer require hhxsv5/laravel-s
```

- 安装完成之后发布配置文件

> 该扩展包具备自动发现功能（Laravel 5.5 以上版本可用），无需手动在 `config/app.php` 配置文件中注册，安装完成后，运行如下 Artisan 命令相应脚本和配置文件发布到根目录下：

```shell
php artisan laravels publish
# 该命令会发布配置文件 laravels.php 到 config 目录下，以及脚本文件到 bin 目录下：
yaoliuyang@benben:~/公共的/phpProject/laravel_study$ php artisan laravels publish
Copied file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/config/laravels.php] To [/home/yaoliuyang/公共的/phpProject/laravel_study/config/laravels.php]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/laravels] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/laravels]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/fswatch] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/fswatch]
Linked file [/home/yaoliuyang/公共的/phpProject/laravel_study/vendor/hhxsv5/laravel-s/bin/inotify] To [/home/yaoliuyang/公共的/phpProject/laravel_study/bin/inotify]    
```

- ### 启动 LaravelS

```shell
php bin/laravels start
# 启动后的命令
yaoliuyang@benben:~/公共的/phpProject/laravel_study$ php bin/laravels start
 _                               _  _____ 
| |                             | |/ ____|
| |     __ _ _ __ __ ___   _____| | (___  
| |    / _` | '__/ _` \ \ / / _ \ |\___ \ 
| |___| (_| | | | (_| |\ V /  __/ |____) |
|______\__,_|_|  \__,_| \_/ \___|_|_____/ 
                                           
Speed up your Laravel/Lumen
>>> Components
+---------------------------+---------+
| Component                 | Version |
+---------------------------+---------+
| PHP                       | 7.4.3   |
| Swoole                    | 4.6.7   |
| LaravelS                  | 3.7.20  |
| Laravel Framework [local] | 8.53.1  |
+---------------------------+---------+
>>> Protocols
+-----------+--------+-------------------+-----------------------+
| Protocol  | Status | Handler           | Listen At             |
+-----------+--------+-------------------+-----------------------+
| Main HTTP | On     | Laravel Framework | http://127.0.0.1:5200 |
+-----------+--------+-------------------+-----------------------+
>>> Feedback: https://github.com/hhxsv5/laravel-s
[2021-08-15 12:37:07] [TRACE] Swoole is running, press Ctrl+C to quit.
```

- laravel 启动命令解释

![命令](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/35dcac73c81020ff6c06e346563fcc34.jpg)

```php
  /**
   * 这样，Swoole 服务就被启动起来，监听本地的 5200 端口，如果有请求发送到这个端口，它就可以进行处理。
   * 此外 php bin/laravels 还支持其它命令对 LaravelS 进行管理：
   */
# 端口可以在config\laravels.php中的进行修改
'listen_port' => env('LARAVELS_LISTEN_PORT', 5200),# 修改laravels监听启动端口    
```

- 生产环境请使用守护进程

> #### 通过 Supervisor 管理 LaravelS
>
> 如果是在生产环境使用，推荐使用 Supervisor 对 LaravelS 服务进行管理，从而提供服务的稳定性，相应的配置示例如下：

```shell
[program:laravel-s-test]
command=/user/local/bin/php /path/to/project/bin/laravels start -i
numprocs=1
autostart=true
autorestart=true
startretries=3
user=www-data
redirect_stderr=true
stdout_logfile=/path/to/project/storage/logs/supervisord-stdout.log
# 其中 /path/to/project 为 Web 项目的目录，你可以根据自己的项目路径进行修改。
```

