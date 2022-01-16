# 讲解

> 通过 --link 可以解决网络联通问题(通过名字就可以访问) **例如ping php**

**拉取php镜像** && nginx 镜像

```dockerfile
[root@VM-16-5-centos ~]# docker pull php:7.4.3-fpm
[root@VM-16-5-centos ~]# docker nginx
# 启动php
[root@VM-16-5-centos ~]# docker run --name  myphp-fpm -v ~/nginx/www:/www  -d php:7.4.3-fpm
# 查看启动的php 可以发现已经开启了9000 tcp端口
[root@VM-16-5-centos ~]# docker ps -a | grep myphp-fpm
CONTAINER ID        IMAGE                                   COMMAND                  CREATED             STATUS              PORTS                                              NAMES
bf97c4677281        php:7.4.3-fpm                           "docker-php-entryp..."   14 seconds ago      Up 14 seconds       9000/tcp                                           myphp-fpm

# 启动nginx --link myphp-fpm:php \
    docker run --name runoob-php-nginx -p 8083:80 -d \
    -v ~/nginx/www:/usr/share/nginx/html:ro \
    -v ~/nginx/conf/conf.d:/etc/nginx/conf.d:ro \
    --link myphp-fpm:php \
    nginx

# 进入容器后ping php        
root@dcc67001ebbe:/# ping php     
PING php (172.17.0.7) 56(84) bytes of data.
```