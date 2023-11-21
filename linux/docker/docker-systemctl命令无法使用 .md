

# 资料

## 参考资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/f_zyj/article/details/93378737) |
| 第三方博客 | [link](https://www.cnblogs.com/lph970417/p/14754072.html)    |

# 方案

```php
docker容器中安装了一些服务之后需要将其启动，但执行systemctl命令之后却提示如下报错：
System has not been booted with systemd as init system (PID 1). Can’t operate.
Failed to connect to bus: Host is down

原因是 1号进程不是 init ，而是其他例如 /bin/bash ，所以导致缺少相关文件无法运行。

解决方案：/sbin/init

例如：centos

docker run -d --name test --privileged=true centos /sbin/init
docker exec -it test /bin/bash

PS:–privilaged=true一定要加上的。
```

