

# 一、资料

| name       | url                                                  |
| ---------- | ---------------------------------------------------- |
| 第三方博客 | [link](https://www.cnblogs.com/asxf/p/11158912.html) |
|            |                                                      |



## 使用

- 启动容器的时候添加数据卷

 `docker run -it -v /宿主机绝对路径:/容器内目录：权限   镜像名`

```shell
docker run -itd --name php -v /etc/php:/usr/local/etc/php eb

# windows 本地目录\etc\nginx要使用反斜杠 \
docker run -itd --name nginx -p 8080:80  -v c:\etc\nginx:/www 4f
```

