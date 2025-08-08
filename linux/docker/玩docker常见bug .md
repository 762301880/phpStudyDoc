  

# [删除容器内部删除映射文件报错](https://blog.csdn.net/qq_33801641/article/details/109571834?spm=1001.2101.3001.6650.9&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-9.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-9.pc_relevant_aa&utm_relevant_index=13)

**报错示例**

```shell
bash-5.0# cd /data/work/
bash-5.0# ls
laravel_study
bash-5.0# rm -rf laravel_study/
rm: can't remove 'laravel_study': Resource busy
```

**情况描述**

> **docker run -v /data/work/laravel:/data/work/laravel -p 9501:9501 -p 1997:80 -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole**
>
> 以上是我启动后-v 不小心指定的映射目录问题是 我只想在work下当作工作目录 看着下一级目录有点烦
>
> 原因是 -v 指定的目录不可以删除 

 **解决方案 放弃这个容器直接重新指定映射目录** 

> 删除原有容器后重新指定映射目录
>
> **docker run -v /data/work/:/data/work/  -p 9501:9501 -p 1997:80 -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole**



# 容器内部时区与服务器不一致

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.cnblogs.com/double-dong/p/11533341.html) [link](https://m.php.cn/article/486823.html) |

**修改示例**

```shell
# 查询当前时区
bash-5.0# date -R
Mon, 21 Mar 2022 07:44:02 +0000
# 复制相应的时区文件，替换系统时区文件； 如果容器内部没有这个文件可以到本地服务器对应目录下然后拷到容器内部替换
bash-5.0# cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
# 再次查询时区
bash-5.0# date -R
Mon, 21 Mar 2022 15:45:04 +0800

```

# 容器内部定时任务无法启用的问题

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_53357266/article/details/118016035) |

**解决方案**

```shell
# 查看进程位置
bash-5.0# find / -name *cron\*
/etc/crontabs
/root/.composer/cache/repo/https---repo.packagist.org/provider-dragonmantank~cron-expression.json
/root/.composer/cache/repo/https---repo.packagist.org/provider-dragonmantank~cron-expression~dev.json
/usr/share/vim/vim82/syntax/crontab.vim
/usr/sbin/crond   # 这个就是进程启动的目录
/usr/bin/crontab 
/run/crond.reboot
/run/crond.pid
/var/spool/cron
/var/spool/cron/crontabs
/data/work/vendor/dragonmantank/cron-expression

# 容器内启动定时任务  这里记住不能多次启动不然就会像下面一样可以开启多个定时任务进程(例如同时开了三个账号给同一个人发信息)
bash-5.0# crond
# 查看启动的定时任务进程
bash-5.0# ps aux|grep cron
  546 root      0:00 crond
  550 root      0:00 crond
  554 root      0:00 crond
  556 root      0:00 grep cron
  # 杀死多余的进程 kill 进程号
  kill 546
```

## **补充**

**关于laravel 框架使用定时任务执行不一致的问题**

> 事情缘由，我想在laravel框架的任务调度中每天给自己推送心灵鸡汤(**->dailyAt('10:10'); 每天十点10分推送鸡汤**)，
>
> 问题就是，好不容易把容器的时区调整好了
>
> 但是项目中定时定时任务却总是玩了几个小时或者早了几个小时，因该不是容器内部的原因，突然想起来laravel好像可以
>
> 配置时区，默认使用的好像是**UTC**世界时区，抱着试一试的心态我修改了一下时区为**上海时区**果然执行成功

**修改时区**

```php
# 找到config\app.php 文件
# 'timezone' => 'UTC'  修改为
'timezone' => 'Asia/Shanghai',   
```

**建议写一个测试文件用于监控定时任务**

```shell
# test  每分钟跑一个定时脚本写入时间  每小时10分钟清理只写入一条
* * * * *   echo  $(date "+%Y-%m-%d %X") >> ~/test_cron.txt 
10 * * * *  echo  $(date "+%Y-%m-%d %X")  >> ~/test_cron.txt 
```

#  mount挂载报错mount failed: Operation not permitted.

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://huaweicloud.csdn.net/63311178d3efff3090b51123.html) |

```shell
# 启动docker的时候加上命令   --privileged=True就OK
```

# 不同容器写日志权限问题



**php容器Cli生成的文件**

> **laravel-2025-08-08.log**这是一个由root用户拥有的 Laravel 日志文件，权限为所有者可读写、所属组和其他用户仅可读
>
> 例如 调用 php artisan 自定义命令写入的日志 

```shell
root@hcss-ecs-739f:/work/php_projects/laravel_study/storage/logs# ls -al
total 100
drwxrwxrwx 2 www-data www-data  4096 Aug  8 08:10 .
drwxrwxrwx 5 root     root      4096 Aug  7 09:53 ..
...........
-rwxrwxrwx 1 root     root     26077 Aug  7 23:00 laravel-2025-08-07.log
-rw-r--r-- 1 root     root       176 Aug  8 09:01 laravel-2025-08-08.log
```

**nginx生成的文件**

> 把上面一个文件删了调用接口 会通过 ip+地址 会调用 php-fpm  生成一个  nginx用户生成的日志文件

```shell
root@hcss-ecs-739f:/work/php_projects/laravel_study/storage/logs# ls -al
total 100
...........
-rwxrwxrwx 1 root     root     26077 Aug  7 23:00 laravel-2025-08-07.log
-rw-r--r-- 1 www-data www-data   154 Aug  8 09:34 laravel-2025-08-08.log
```





## 解决方案

### Laravel 文件配置调整:

在`config/logging.php`中设置日志文件的权限，避免新文件被创建时权限不足：

> 这样其他用户就有写入的权限了
>
> 生成的日志 
>
> 第 8-10 个字符：其他用户（other）权限
> `rw-` 表示所有用户都拥有**读（r）** 和**写（w）** 权限，无执行（x）权限。
>
> 权限总结：该文件对所有用户开放读写权限，无执行权限。

```php
'permission' => 0666, // 允许所有用户读写


# 生成的日志大概这样   
-rw-rw-rw- 1 root root  701 Aug  8 11:01 laravel-2025-08-08.log
```