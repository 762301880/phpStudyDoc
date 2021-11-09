# 说明

> 使用swoole的时候经常会看见端口被占用的情况如下所示

```shell
bash-5.0# php bin/hyperf.php start

In Server.php line 170:
failed to listen server port[0.0.0.0:9802], Error: Address in use[98]
```

## 解决方法

**查看被占用的端口**

```shell
bash-5.0# netstat  -anp  |  grep 9802
tcp        0      0 0.0.0.0:9802            0.0.0.0:*               LISTEN      16/skeleton.Master
```

**杀死进程**

> 使用***kill***杀死进程

```shell
bash-5.0# kill 16
bash-5.0# php bin/hyperf.php start
[INFO] Worker#0 started.
[INFO] HTTP Server listening at 0.0.0.0:9802
[INFO] Worker#1 started.
[INFO] Process[crontab-dispatcher.0] start.
[INFO] Process[queue.default.0] start.
string(31) "sourceCount_2021-11-09 11:29:01"
```

