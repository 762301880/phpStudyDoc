## [安装sshd](https://cloud.tencent.com/developer/article/1683604)

```shell
apk add openssh
```

##  安装docker

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://blog.csdn.net/juesystem/article/details/112981830) |

###  添加镜像源安装

```shell
    echo http://dl-cdn.alpinelinux.org/alpine/latest-stable/community >> /etc/apk/repositories  # 添加镜像源
apk update  # 更新镜像源

# 搜索docker镜像
localhost:/etc/nginx/http.d# apk search docker
docker-bash-completion-20.10.21-r2
docker-cli-20.10.21-r2
podman-docker-4.3.1-r1
openvswitch-2.17.3-r0
.........
```

###  alpine 启动docker 

```shell
service docker start      # 启动
service docker restart    # 重启
service docker stop       # 停止
```

