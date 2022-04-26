# 说明

> 实际开发过程中经常会后台一直执行某些命令问题就是不可能一直停留在这个界面
>
> 万一中途命令中断了也是一个问题

# 资料

| 名称                             | 链接                                                         |
| -------------------------------- | ------------------------------------------------------------ |
| Supervisor-官方文档              | [链接](http://www.supervisord.org/index.html)                |
| laravel supervisor介绍           | [链接](https://learnku.com/docs/laravel/8.x/queues/9398#e45763) |
| 书栈(守护进程二三事与Supervisor) | [link](https://www.bookstack.cn/read/swoole_study/%E7%95%AA%E5%A4%96%EF%BC%9A%E5%AE%88%E6%8A%A4%E8%BF%9B%E7%A8%8B%E4%BA%8C%E4%B8%89%E4%BA%8B%E4%B8%8ESupervisor.md) |
| 第三方博客参考                   | [链接](https://www.jianshu.com/p/0036e8e6b882) [链接](https://www.cnblogs.com/yezigege/p/13530850.html) [链接](https://blog.51cto.com/lixcto/1539136) [链接](https://www.cnblogs.com/zhoujinyi/p/6073705.html) [链接](https://www.it610.com/article/1305823479957852160.htm) |

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

# 文件中加入
[program:laravel_study_socket]
process_name=%(program_name)s_%(process_num)02d  # 进程名，当下面的numprocs为多个的时候，才需要。否则默认
command=php /data/work/laravel_study/artisan swoole
autostart=true   # true表示supervisord启动时自动启动
autorestart=true  # 是否自动重启
user=root # 修改为当前登录的用户 可以用 whoami 命令查询
numprocs=3 #启动这个程序的多个实例，如果numprocs>1，则process_name的表达式必须包含%(process_num)s，默认是1
redirect_stderr=true #如果为true，则stderr的日志会被写入stdout日志文件中  默认为false，非必须设置
stdout_logfile=/data/logs/worker.log # 日志保存位置 可以自定义
stopwaitsecs=3600 # 停止等待秒数


# 关键如果修改了配置文件需要 https://blog.csdn.net/wangjianwanxiao/article/details/51007354
supervisorctl reload  # 重载配置文件
# 如果重启完报错 https://blog.csdn.net/xwd127429/article/details/106103653
error: <class 'xmlrpc.client.Fault'>, <Fault 6: 'SHUTDOWN_STATE'>: file: /usr/lib/python3.8/xmlrpc/client.py line: 655
supervisord -c /etc/supervisord.conf # 启动supervisor 使用该命令启动supervisor

# 查看是否启动成功
supervisorctl status
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
user=root # 修改为当前登录的用户 可以用 whoami 命令查询
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

# 配合jenkins使用supervisor

> 开发过程中我们使用jenkins自动上传代码的时候不想再容器内部手动执行**supervisorctl restart 配置名称** 
>
> 重启命令这个时候可以结合jenkins自动刷新

**脚本**

```shell
docker exec laravel_study supervisorctl restart laravel_swoole
```



![image-20220113150753501](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220113150753501.png)
