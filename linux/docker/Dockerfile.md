# 说明& 资料

## 说明

> 平时我们需要构建自己的镜像需要在已经启动的容器内添加自己需要的软件,再
>
> 提交(commit)为镜像，然后提交到自己的dockerHub中以便下次继续使用，官方不推荐
>
> 这个使用方法于是提供了更简便的使用方法dockerfile,直接把我们需要的环境与配置写在文件
>
> 中直接一个命令启动即可构建出我们需要的开发环境

## 资料

| name                     | url                                                          |
| ------------------------ | ------------------------------------------------------------ |
| 使用 Dockerfile 定制镜像 | [link](https://yeasy.gitbook.io/docker_practice/image/build) |
|                          |                                                              |

Dockefile的主体包括如下几个部分

```shell
FROM rackspacedot/python37:latest #指明基础镜像包
COPY xxx /xxx/ #将Dockerfile同目录下的xxx文件或目录拷贝到生成镜像中的/xxx/目录下
RUN xxx #这里相当于在bash里执行指令，每条指令用一个RUN来标记，完成service文件的chmod修改等
MAINTAINER name email #留下你的大名和邮箱
EXPOSE 10000 #暴露端口号
ENTRYPOINT xxx #这个是启动执行命令，只能有一条
CMD xxx #这个也是启动执行命令（或给ENTRYPOINT传递默认参数），若启动容器时附加了参数，则CMD中的命令会被忽略
```



# 使用

## 创建一个dockerfile

>1、下面以定制一个 nginx 镜像（构建好的镜像内会有一个 /usr/share/nginx/html/index.html 文件）
>
>在一个空目录下，新建一个名为 Dockerfile 文件，并在文件内添加以下内容：

```shell
mkdir mynginx
cd mynginx
touch Dockerfile
vim Dockerfile  # 编辑Dockerfile
# 在里面输入
FROM nginx
RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html
```

## 构建一个基本镜像

- 在docker目录中打开终端执行命令

```shell
docker build -t   nginx:latest  .
```

