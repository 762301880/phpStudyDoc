#  docker  安装mysql

[docker-mysql官网](https://registry.hub.docker.com/_/mysql)

```shell
# 下载 
docker pull mysql
# 启动 -e MYSQL_ROOT_PASSWORD=指定密码
docker run --name mysql -itd -p 3306:3306 -p 9702:9702 -v /data/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=yaoliuyang  容器id
# 执行上一步直接远程连接即可 用户名是root
```

