#  docker  安装mysql

## 资料

| 名称                     | 地址                                                         |
| ------------------------ | ------------------------------------------------------------ |
| 菜鸟教程-docker安装mysql | [link](https://www.runoob.com/docker/docker-install-mysql.html) |
|                          |                                                              |

[docker-mysql官网](https://registry.hub.docker.com/_/mysql)

```shell
# 下载 默认下载得是最新版 8.*的mysql
docker pull mysql
# 启动 -e(设置环境变量) MYSQL_ROOT_PASSWORD=指定密码 
docker run --name mysql -itd -p 3306:3306 -p 9702:9702 -v /data/mysql:/etc/mysql -e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
# 执行上一步直接远程连接即可 用户名是root
```

# bug解析

## mysql8以上遇到`The server requested authentication method unknown to the client`

**亲测不行日后再想办法解决**

[参考](https://blog.csdn.net/maoxinwen1/article/details/88629313)

> 修改**mysql.cnf** 配置默认身份验证插件
>
> 找到容器内部`/etc/mysql/conf.d`下的**mysql.cnf**配置使用`vim`打开

```mysql
[mysqld]
default_authentication_plugin = mysql_native_password
```

**docker创建mysql容器时挂载文件路径后无法启动已解决**

```php
# 在docker run中加入 --privileged=true  给容器加上特定权限

docker run --name mysql -itd --privileged=true -p 3306:3306 -p 9702:9702 -v /data/mysql:/etc/mysql 
-e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
