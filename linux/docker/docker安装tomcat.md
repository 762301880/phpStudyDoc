# 资料

| name                     | url                                     |
| ------------------------ | --------------------------------------- |
| dockerhub-tomcat镜像地址 | [link](https://hub.docker.com/_/tomcat) |
|                          |                                         |



# 安装

```shell
 # 官方的使用
 docker run -it --rm tomcat:9.0
 # 我们之前的启动都是后台，停止了容器之后，容器还是可以查到
 --rm 用完之后直接删除不会查询到，一般用来测试用完即删
 
 # 正常使用
 
 # 下载镜像
 docker pull tomcat:9.0
 # 启动镜像
 docker run -itd --name tomcat -p 8088:8080 镜像id
```

## 此时访问网站404

> 由于容器是阉割版，所以没有webapps 
>
> webapps 项目部署目录

- 以下操作即可

```shell
root@e5f7f01a6d44:/usr/local/tomcat# cd /usr/local/tomcat/  
root@e5f7f01a6d44:/usr/local/tomcat# cp webapps.dist/* webapps
cp: -r not specified; omitting directory 'webapps.dist/ROOT'
cp: -r not specified; omitting directory 'webapps.dist/docs'
cp: -r not specified; omitting directory 'webapps.dist/examples'
cp: -r not specified; omitting directory 'webapps.dist/host-manager'
cp: -r not specified; omitting directory 'webapps.dist/manager'
root@e5f7f01a6d44:/usr/local/tomcat# cp -r  webapps.dist/* webapps
```

