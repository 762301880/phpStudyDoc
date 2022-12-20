



**资料**

| 名称            | 地址                                                         |
| --------------- | ------------------------------------------------------------ |
| openrc-官方文档 | [link](https://docs.alpinelinux.org/user-handbook/0.1a/Working/openrc.html) |
| 网络博客        | [link](https://www.kryii.com/44.html)  [link](http://iytc.net/wordpress/?p=5333) [link](https://www.onitroad.com/jc/linux/how-to-enable-and-start-services-on-alpine-linux.html) [link](https://www.xiexianbin.cn/docker/images/docker-alpine/index.html?to_index=1) |



**安装openrc**

```shell
apk add openrc  # 安装openrc
```

**添加系统启动时候的自启服务**

> **<font color="red">经过测试此启动只针对于纯alpine系统有效,docker-alpine系统镜像并不会生效</font>**

```shell
rc-update add {service-name} {run-level-name}            #  service-name 需要启动的服务名称  run-level-name 运行的级别名称

# 例子
rc-update add nginx default
```

### 查看手动启动的服务

```shell
rc-status --manual
```

### 查看崩溃的服务

```shell
rc-status --crashed
```

## Alpine Linux如何列出所有可用服务

执行以下命令：

```shell
rc-service --list
# 或
rc-service --list | grep -i nginx
```

## 查看服务列表

执行以下命令：

```shell
rc-status --list
```

可以使用rc命令更改运行级别：

```shell
rc {runlevel}
# 更改级别
rc boot
rc default
rc shutdown
```

## 启用/停止/重启服务

```shell
rc-service Service名 start/stop/restart
```

##  补充

### 开启openrc日志

> 编辑**/etc/rc.conf** 配置文件

```shell
# vim /etc/rc.conf

# rc_logger launches a logging daemon to log the entire rc process to
# /var/log/rc.log
# NOTE: Linux systems require the devfs service to be started before
# logging can take place and as such cannot log the sysinit runlevel.
rc_logger="YES" # 取消注释修改为YES

# Through rc_log_path you can specify a custom log file.
# The default value is: /var/log/rc.log
rc_log_path="/var/log/rc.log" # 取消注释
```

