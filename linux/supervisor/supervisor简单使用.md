# 说明

> 实际开发过程中经常会后台一直执行某些命令问题就是不可能一直停留在这个界面
>
> 万一中途命令中断了也是一个问题

# 资料

| 名称                                   | 链接                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| Supervisor-官方文档  第三方中文文档-RD | [链接](http://www.supervisord.org/index.html)  [链接](https://www.rddoc.com/doc/Supervisor/3.3.1/zh/#api-documentation) |
| hyperf-Supervisor                      | [链接](https://hyperf.wiki/2.2/#/zh-cn/tutorial/supervisor)  |
| laravel supervisor介绍                 | [链接](https://learnku.com/docs/laravel/8.x/queues/9398#e45763) |
| 书栈(守护进程二三事与Supervisor)       | [链接](https://www.bookstack.cn/read/swoole_study/%E7%95%AA%E5%A4%96%EF%BC%9A%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B%E4%BA%8C%E4%B8%89%E4%BA%8B%E4%B8%8ESupervisor.md) |
| 第三方博客参考                         | [链接](https://www.jianshu.com/p/0036e8e6b882) [链接](https://www.cnblogs.com/yezigege/p/13530850.html) [链接](https://blog.51cto.com/lixcto/1539136) [链接](https://www.cnblogs.com/zhoujinyi/p/6073705.html)  [链接](https://blog.csdn.net/weixin_37008947/article/details/108107216) |

# 安装

## alipine 安装

```shell
apk add supervisor # apk 命令安装会同时安装依赖
apk del supervisor # 卸载supervisor

# 查看版本
supervisord -v
```

# [基本使用](https://blog.csdn.net/SooKie_p/article/details/109526417?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&utm_relevant_index=1)

创建配置文件

Supervisor 安装完成后，运行 `echo_supervisord_conf`。这会将“示例”Supervisor 配置文件打印到终端的标准输出。

**配置文件位置**

> 安装完成之后会在 **/etc/supervisord.conf** 中有配置文件(**注意这里修改了一定要使用supervisorctl reload 来重新加载配置文件，否则不会生效**)

```shell
# 查看配置文件
cat supervisord.conf

···
[include]
files = /etc/supervisor.d/*.ini # 主要关注这一行这个是我们要添加配置的位置,可见我们的配置文件都在supervisor.d下的*.ini
····

# 添加配置
touch /run/supervisord.conf
supervisord -c /etc/supervisord.conf #可以直接执行这条命令, 自动创建supervisor.sock

mkdir /etc/supervisor.d && chmod -R 777 /etc/supervisor.d # 创建配置文件夹

# 创建配置文件
vim /etc/supervisor.d/文件名称.ini # 例如 vim /etc/supervisor.d/laravel_study_socket.ini
#********************************配置文件开始位置***************************************文件中加入
[program:laravel_study_socket]
# 进程名，当下面的numprocs为多个的时候,才需要。否则默认
process_name=%(program_name)s_%(process_num)02d  
# 需要执行的命令
command=php /data/work/laravel_study/artisan swoole
# true表示supervisord启动时自动启动
autostart=true  
# 是否自动重启
autorestart=true  
# 修改为当前登录的用户 可以用 whoami 命令查询
user=root 
# 启动后，程序需要保持运行以考虑启动成功（将进程从 STARTING 状态移动到 RUNNING 状态）的总秒数。设置为 0 以指示程序不需要保持运行任何特定的时间量。
startsecs=0
#启动这个程序的多个实例，如果numprocs>1,则process_name的表达式必须包含%(process_num)s,默认是1
numprocs=3 
#如果为true,则stderr的日志会被写入stdout日志文件中  默认为false,非必须设置
redirect_stderr=true 
# 日志保存位置 可以自定义
stdout_logfile=/data/logs/worker.log 
 # 停止等待秒数
stopwaitsecs=3600
#********************************配置文件结束位置***************************************


# 关键问题如果修改了配置文件需要重启配置文件 https://blog.csdn.net/wangjianwanxiao/article/details/51007354
supervisorctl reload  # 重载配置文件
# 如果重启完报错 https://blog.csdn.net/xwd127429/article/details/106103653
error: <class 'xmlrpc.client.Fault'>, <Fault 6: 'SHUTDOWN_STATE'>: file: /usr/lib/python3.8/xmlrpc/client.py line: 655
supervisord -c /etc/supervisord.conf # 启动supervisor 使用该命令启动supervisor

# 查看是否启动成功
supervisorctl status
```

## [启动 Supervisor](https://www.topgoer.com/beego%E6%A1%86%E6%9E%B6/%E5%BA%94%E7%94%A8%E9%83%A8%E7%BD%B2/supervisor%E9%83%A8%E7%BD%B2.html)
创建了配置文件后，你可以使用以下命令更新 Supervisor 配置并启动进程：

```shell
Supervisord 安装完成后有两个可用的命令行 supervisord 和 supervisorctl，命令使用解释如下：

supervisord，初始启动 Supervisord  #启动、管理配置中设置的进程。

supervisorctl start programxxx  # 启动某个进程
supervisorctl start all  # 启动全部进程

supervisorctl restart programxxx # 重启某个进程
supervisorctl restart all # 重启全部的进程

supervisorctl stop groupworker: #重启所有属于名为 groupworker 这个分组的进程(start,restart 同理)
supervisorctl stop programxxx  #停止某一个进程(programxxx)，programxxx 为 [program:beepkg] 里配置的值，这个示例就是 beepkg。
supervisorctl stop all  # 停止全部进程，注：start、restart、stop 都不会载入最新的配置文件。

supervisorctl reload  #载入最新的配置文件，停止原有进程并按新的配置启动、管理所有进程。
supervisorctl update  #根据最新的配置文件，启动新配置或有改动的进程，配置没有改动的进程不会受

# 子进程重启(向我上面的就属于子进程	) 
supervisorctl restart swoole:* # 操作组中的所有进程 stop start  
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
startsecs=0
# 修改为当前登录的用户 可以用 whoami 命令查询
user=root 
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log 
stopwaitsecs=3600

user ，它的值默认是 forge 。应该将这个值改为我们系统当前登录的用户
```

## [报错 Error: Another program is already listening on a port that one of our HTTP servers is configured to use.  Shut this program down first before starting supervisord](https://oldtang.com/2477.html)

> 意思是**另一个程序已经在监听我们的HTTP服务器配置使用的端口。在启动监控程序之前先关闭这个程序** 

```shell
ps aux | grep supervisord #首先查看进程号 然后 kill 进程 (这里不推荐这样操作)

# 解决方案
bash-5.0# supervisorctl  # 进入命令模式
# 查看可以操作命令
supervisor> help 
default commands (type help <topic>):
=====================================
add    exit      open  reload  restart   start   tail   
avail  fg        pid   remove  shutdown  status  update 
clear  maintail  quit  reread  signal    stop    version
# 操作命令启动停止等
supervisor> start 
Error: start requires a process name
start <name>		Start a process
start <gname>:*		Start all processes in a group
start <name> <name>	Start multiple processes or groups
start all		Start all processes

```

## 报错 supervisorctl start 项目名称 ERROR (no such process)

> 这个百分之百不要怀疑绝逼是配置文件没有加载  网上说是supervisorctd 没有重启 无奈我重启了docker
>
> 刷新配置命令 **supervisorctl updat**  **supervisorctl reload**

```shell
# 自己的配置
[program:laravel_swoole] 
process_name=%(program_name)s
command=php /data/work/laravel_study/artisan swoole
autostart=true
autorestart=true
startsecs=0
user=root
#numprocs=1
redirect_stderr=true
stdout_logfile=/data/worker.log
stopwaitsecs=3600

# 刷新之后重启
bash-5.0# supervisorctl restart laravel_swoole
laravel_swoole: stopped
laravel_swoole: started
```

## 报错 **unix:///run/supervisord.sock refused connection**

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/qq422431474/article/details/106713757) |

**解决方案**

```shell
supervisord -c /etc/supervisord.conf   # 启动supervisord并使用配置
```

## 使用supervisorctl命令结果发现`FATAL Exited too quickly (process log may have details)`

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_44439488/article/details/115378194) |

**解决方案**

```php
# 配置文件中添加
//.........
startsecs=0 
```



# 配合**jenkins**使用**supervisor**

> 开发过程中我们使用jenkins自动上传代码的时候不想再容器内部手动执行**supervisorctl restart 配置名称** 
>
> 重启命令这个时候可以结合jenkins自动刷新

**脚本**

```shell
docker exec -it laravel_study supervisorctl restart laravel_swoole
```



![image-20220113150753501](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220113150753501.png)

# 重要事件

## 新添加配置一定要重新载入配置新的配置才会生效

## 添加配置文件时候注意事项(重要)

#### 复制配置文件时候一定要注意千万别吗进程名字复制跟之前一样记得修改配置

```shell
[program:jz_order_share_append]  # 进程名称配置配置错误   应该修改为  jz_send_order_give_coupon
process_name=%(program_name)s_%(process_num)02d
command=php /www/wwwroot/home_train/think queue:work --daemon --queue send_order_give_coupon
autostart=true
autorestart=true
startsecs=0
# 修改为当前登录的用户 可以用 whoami 命令查询
user=root
numprocs=1
redirect_stderr=true
stdout_logfile=/etc/supervisord.d/log/jz_send_order_give_coupon.log
stopwaitsecs=3600



# 例如上面的错误配置 我查询为什么我 jz_order_share_append.ini中的命令没跑起来  查看下面的进程被 6333占用了

[root@iZwz9fhv99le4f935sqr8rZ log]# supervisorctl status
cashier                                                                        RUNNING   pid 6313, uptime 0:00:58
cashier-center                                                                 RUNNING   pid 6314, uptime 0:00:58
cashier2                                                                       RUNNING   pid 6315, uptime 0:00:58
gopxs_test                                                                     RUNNING   pid 6321, uptime 0:00:58
gw-erm-api-server                                                              RUNNING   pid 6327, uptime 0:00:58
jz_order_share_append:jz_order_share_append_00                                 RUNNING   pid 6333, uptime 0:00:58
jz_order_success_callback:jz_order_success_callback_00                         RUNNING   pid 6337, uptime 0:00:58
jz_reserve_to_aunt:jz_reserve_to_aunt_00                                       RUNNING   pid 6341, uptime 0:00:58
jz_send_order_service_reserve_notice:jz_send_order_service_reserve_notice_00   RUNNING   pid 6346, uptime 0:00:58
live-connect                                                                   RUNNING   pid 6347, uptime 0:00:58
mid                                                                            RUNNING   pid 6349, uptime 0:00:58
mid2                                                                           RUNNING   pid 6352, uptime 0:00:58
push_erm_api_server                                                            RUNNING   pid 6353, uptime 0:00:58
queue_erm_server                                                               RUNNING   pid 6355, uptime 0:00:58
queue_server                                                                   RUNNING   pid 6356, uptime 0:00:58
reset_top_and_sort                                                             RUNNING   pid 6359, uptime 0:00:58
time_tasks                                                                     RUNNING   pid 6360, uptime 0:00:58
vip_shop_update_time                                                           RUNNING   pid 6361, uptime 0:00:58
# 再次查看6333进程 可以发现另一个复制的守护进程中的配置文件写错
[root@iZwz9fhv99le4f935sqr8rZ log]# ps -ef | grep 6333
root      6333  3449  0 10:45 ?        00:00:00 php /www/wwwroot/home_train/think queue:work --daemon --queue send_order_give_coupon
root      6644  1758  0 10:47 pts/1    00:00:00 grep --color=auto 6333

```

