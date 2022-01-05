# 说明

> 实际开发过程中经常会后台一直执行某些命令问题就是不可能一直停留在这个界面
>
> 万一中途命令中断了也是一个问题

# 资料

| 名称                   | 链接                                                         |
| ---------------------- | ------------------------------------------------------------ |
| Supervisor-官方文档    | [链接](http://www.supervisord.org/index.html)                |
| laravel supervisor介绍 | [链接](https://learnku.com/docs/laravel/8.x/queues/9398#e45763) |

# 安装

## alipine 安装

```shell
apk add supervisor # apk 命令安装会同时安装依赖
apk del supervisor # 卸载supervisor
```

# [基本使用](https://blog.csdn.net/SooKie_p/article/details/109526417?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&utm_relevant_index=1)

创建配置文件

Supervisor 安装完成后，运行 `echo_supervisord_conf`。这会将“示例”Supervisor 配置文件打印到终端的标准输出。

**配置文件位置**

> 安装完成之后会在 **/etc/supervisord.conf** 中有配置文件

```shell
# 查看配置文件
cat supervisord.conf

···
[include]
files = /etc/supervisor.d/*.ini # 主要关注这一行这个是我们要添加配置的位置
····


# 添加配置
touch /run/supervisord.conf
supervisord -c /etc/supervisord.conf #可以直接执行这条命令, 自动创建supervisor.sock

mkdir /etc/supervisor.d && chmod -R 777 /etc/supervisor.d # 创建配置文件夹

# 创建配置文件
vim /etc/supervisor.d/文件名称.ini # 例如 vim /etc/supervisor.d/laravel_study_socket.ini

# 文件中加入
[program:laravel_study_socket]
process_name=%(program_name)s_%(process_num)02d
command=php /data/work/laravel_study/artisan swoole
autostart=true
autorestart=true
user=root # 修改为当前登录的用户 可以用 whoami 命令查询
numprocs=8
redirect_stderr=true
stdout_logfile=/data/logs/worker.log # 日志保存位置 可以自定义
stopwaitsecs=3600


# 关键如果修改了配置文件需要 https://blog.csdn.net/wangjianwanxiao/article/details/51007354
supervisorctl reload  # 重载配置文件
# 如果重启完报错 https://blog.csdn.net/xwd127429/article/details/106103653
error: <class 'xmlrpc.client.Fault'>, <Fault 6: 'SHUTDOWN_STATE'>: file: /usr/lib/python3.8/xmlrpc/client.py line: 655
supervisord -c /etc/supervisord.conf

```

[启动 Supervisor](https://www.topgoer.com/beego%E6%A1%86%E6%9E%B6/%E5%BA%94%E7%94%A8%E9%83%A8%E7%BD%B2/supervisor%E9%83%A8%E7%BD%B2.html)
创建了配置文件后，你可以使用以下命令更新 Supervisor 配置并启动进程：

```shell
Supervisord 安装完成后有两个可用的命令行 supervisord 和 supervisorctl，命令使用解释如下：

supervisord，初始启动 Supervisord  #启动、管理配置中设置的进程。
supervisorctl stop programxxx  #停止某一个进程(programxxx)，programxxx 为 [program:beepkg] 里配置的值，这个示例就是 beepkg。
supervisorctl start programxxx  # 启动某个进程
supervisorctl restart programxxx # 重启某个进程
supervisorctl stop groupworker: #重启所有属于名为 groupworker 这个分组的进程(start,restart 同理)
supervisorctl stop all  # 停止全部进程，注：start、restart、stop 都不会载入最新的配置文件。
supervisorctl reload  #载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程。
supervisorctl update  #根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受
```

# 查询命令是否启用成功

```shell
# 查询是否启用成功  netstat -anp | grep 端口号
bash-5.0# netstat -anp | grep 9501
tcp        0      0 0.0.0.0:9501            0.0.0.0:*               LISTEN      4741/php            
# 关闭进程用于测试
bash-5.0# kill 4741
```



# bug 解析

## [Invalid user name forge in section 报错](https://blog.csdn.net/u013866352/article/details/105413123/)

>  修改我们当前需要启动的文件 例如 laravel_study_socket

```shell
[program:laravel_study_socket]
process_name=%(program_name)s_%(process_num)02d
command=php /data/work/laravel_study/artisan swoole
autostart=true
autorestart=true
user=root # 修改为当前登录的用户 可以用 whoami 命令查询
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log 
stopwaitsecs=3600

user ，它的值默认是 forge 。应该将这个值改为我们系统当前登录的用户
```

