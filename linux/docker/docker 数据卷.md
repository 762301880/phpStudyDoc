

# 一、资料

| name       | url                                                  |
| ---------- | ---------------------------------------------------- |
| 第三方博客 | [link](https://www.cnblogs.com/asxf/p/11158912.html) |
|            |                                                      |

## 什么是数据卷

> 我们的程序是运行在docker中的每次修改配置信息都需要进入容器内部修改配置，
>
> 有没有一种方法可以让我们在容器外(本地开发环境)，就可以修改容器内的配置呢？
>
> 问题: 数据如果保存在容器内，那么容器一删除，数据就会丢失，如果将数据挂在到本地
>
> 那么就算容器删除数据也不会丢失,下次启动镜像时直接挂载本地数据即可
>
> 案例：mysql容器如果mysql容器删除了容器内部的数据就会丢失，此问题就是传说中的删库跑路

## 使用数据卷

### 方式一：直接使用命令挂载 `- v`

- 启动容器的时候添加数据卷

 `docker run -it -v /宿主机绝对路径:/容器内目录：权限   镜像名`

```shell
#
docker run -itd --name php -v /etc/php:/usr/local/etc/php 容器id
# windows 本地目录\etc\nginx要使用反斜杠 \
docker run -itd --name nginx -p 8080:80  -v c:\etc\nginx:/www  容器id
```



## 具名挂载&匿名挂载

```shell
# 匿名挂载
-v 容器内路径 # 不指定主机名直接使用容器内的路径
docker run -d -p --name nginx  -v /etc/nginx

# 查看数据卷
[root@VM-24-20-centos ~]# docker volume ls 
DRIVER              VOLUME NAME
local               645e88e6796e3f0e1889b9a2d70e9b107330e37cef134b57be76c0d71168806d
local               f8c9a08b96aa846750cfcf5c6d81bd44e7155df5ccfb0e79d72262a422face0f

```

- 具名挂载

```shell
# yaoliuyangs 注意这里不带/ 只是指定名称
[root@VM-24-20-centos ~]# docker run -d  -v yaoliuyangs:/etc/nginx 4c
795d5d584d3c5310f288667efb3969208da981bda1cd2e6ef8174830897a2974
[root@VM-24-20-centos ~]# docker volume ls
DRIVER              VOLUME NAME
local               645e88e6796e3f0e1889b9a2d70e9b107330e37cef134b57be76c0d71168806d
local               f8c9a08b96aa846750cfcf5c6d81bd44e7155df5ccfb0e79d72262a422face0f
local               yaoliuyangs # 可以看出这里指定可名称
# 查看具体挂载到了那个目录
[root@VM-24-20-centos ~]# docker volume inspect yaoliuyangs
[
    {
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/yaoliuyangs/_data", # 具体挂载的目录
        "Name": "yaoliuyangs",
        "Options": {},
        "Scope": "local"
    }
]
```

- 如何确定是具名挂载还是匿名挂载

```shell
-v 容器内路径  # 匿名挂载
-v 卷名:容器内路经 # 具名挂载
-v /本地路径:容器内路径 # 指定路径挂载
```

拓展

```shell
# 启动nginx  数据卷 :ro 意思是 readonly 只读
[root@VM-24-20-centos ~] docker run -itd --name mynginx -p 8080:80 -v /etc/nginx:/etc/nginx:ro  容器id

# 启动nginx  数据卷 :rw 意思是 readwrite 可
[root@VM-24-20-centos ~] docker run -itd --name mynginx -p 8080:80 -v /etc/nginx:/etc/nginx:rw  容器id

```



## 



































