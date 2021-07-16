

## 拉取镜像 

```shell
docker pull mysql:5.7
```

## 启动mysql实例

```shell
#--name 为mysql的实例设置别名。 -p 3307为对外暴露的端口。3306是内部端口 
#-e MYSQL_ROOT_PASSWORD 设置mysql登录密码  -d 以守护进程运行（后台运行） 最后的mysql是镜像名称
#-v 数据卷挂载
#-p 端口映射
#-it 以命令交互
# -d 守护进程启动
docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=123456 
-v /home/mysql/conf:/etc/mysql/conf.d  -v /home/mysql/data:/var/lib/mysql 
-p 3306:3306 
-itd mysql:5.7
```

