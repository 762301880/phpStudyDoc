



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