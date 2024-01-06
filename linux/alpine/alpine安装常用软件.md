# 资料

| 名称            | 地址                                 |
| --------------- | ------------------------------------ |
| alpine-官方网站 | [link](https://www.alpinelinux.org/) |

#  alipine-php扩展

##  安装php-mysqli扩展

```php
# 安装php-mysqli扩展     apk add php7-mysqli
/**
 * 查询扩展
 */
bash-5.0# apk search mysqli  
php7-mysqli-7.4.26-r1
mariadb-client-10.4.22-r0
/**
 * 安装扩展
 */    
bash-5.0# apk add php7-mysqli
(1/1) Installing php7-mysqli (7.4.26-r1)
OK: 95 MiB in 86 packages    
 /**
 * 查询安装
 */   
 bash-5.0# php -m
[PHP Modules]
.....
mysqli
.....
```

# [安装sshd](https://cloud.tencent.com/developer/article/1683604)

```shell
apk add openssh

# Alpine 默认关闭root用户ssh登录。更改 sshd_config 文件，开启root登录。
echo "PermitRootLogin yes" >> /etc/ssh/sshd_config

# 重启 ssh 服务

service sshd restart

```

# 安装docker

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://blog.csdn.net/juesystem/article/details/112981830) |

## 添加镜像源安装

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

## alpine 启动docker 

```shell
service docker start      # 启动
service docker restart    # 重启
service docker stop       # 停止
```

