  

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

| 名称       | 地址                                                        |
| ---------- | ----------------------------------------------------------- |
| 第三方博客 | [link](https://www.cnblogs.com/double-dong/p/11533341.html) |

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









