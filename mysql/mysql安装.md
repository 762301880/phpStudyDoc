#  docker  安装mysql

[docker-mysql官网](https://registry.hub.docker.com/_/mysql)

```shell
# 下载 默认下载得是最新版 8.*d
docker pull mysql
# 启动 -e MYSQL_ROOT_PASSWORD=指定密码
docker run --name mysql -itd -p 3306:3306 -p 9702:9702 -v /data/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
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

