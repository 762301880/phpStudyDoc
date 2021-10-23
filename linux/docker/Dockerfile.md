# 说明& 资料

## 说明

> 平时我们需要构建自己的镜像需要在已经启动的容器内添加自己需要的软件,再
>
> 提交(commit)为镜像，然后提交到自己的**dockerHub**中以便下次继续使用，官方不推荐
>
> 这个使用方法于是提供了更简便的使用方法**dockerfile**,直接把我们需要的环境与配置写在文件
>
> 中直接一个命令启动即可构建出我们需要的开发环境
>
> ***具体说明***:
>
> ***Dockerfile***就是用来构建docker镜像的构建文件!命令脚本!
>
> ***构建步骤***
>
> 1. 编写一个[^dockerfile]文件
> 2. **docker build** 构建成为一个镜像
> 3. **docker run**运行镜像
> 4. **docker push** 发布镜像([^DockerHub]、[^阿里云镜像仓库])

## 资料

| name                               | url                                                          |
| ---------------------------------- | ------------------------------------------------------------ |
| 使用 **Dockerfile** 定制镜像       | [link](https://yeasy.gitbook.io/docker_practice/image/build) |
| **Docker**学习笔记：**Dockerfile** | [link](https://www.docker.org.cn/dockerppt/114.html)         |
| 官网-dockerfile实战                | [link](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/) |

**Dockefile**的主体包括如下几个部分

>**基础知识**
>
>1. 每个**关键字**(指令)都必须是**大写字母**
>2. 执行顺序**从上**-***到下***执行
>3. **#**表示注释
>4. 每一个**指令**都会**创建**提交一个**新**的**镜像层**，并**提交**

```dockerfile
FROM rackspacedot/python37:latest #指明基础镜像包，一切从这里开始构建
MAINTAINER  name email # 镜像是谁写的，姓名+邮箱(通用标准)
COPY xxx /xxx/ #将Dockerfile同目录下的xxx文件或目录拷贝到生成镜像中的/xxx/目录下
RUN xxx #这里相当于在bash里执行指令，每条指令用一个RUN来标记，完成service文件的chmod修改等，镜像构建的时候需要运行的命令
ADD xxx # 步骤
ENTRYPOINT xxx #这个是启动执行命令，只能有一条，可以追加命令
CMD xxx #这个也是启动执行命令（或给ENTRYPOINT传递默认参数），若启动容器时附加了参数，则CMD中的命令会被忽略,只有最后一个会生效，可被替代
WORKDIT #镜像的工作目录
VOLUME  #挂载的目录
EXPOSE 10000 #暴露端口号,指定端口
ONBUILD #当构建一个被继承 DockerFile这个时候就会运行ONBUILD的指令。触发指令
ENV  # 构建的时候设置环境变量

```

## CMD和ENTRYPOINT区别

```php
CMD            # 指定这个容器启动时候需要运行的命令,只有最后一个会生效,可被代替
ENTRYPOINT     # 指定这个容器启动的时候需要运行的命令,可以追加命令    
```

- 测试**CMD**命令

```dockerfile
# 1. 编写镜像，镜像内容如下 简单 执行一条 显示列表的镜像	
FROM nginx:latest
CMD ["ls","-a"]
# 执行效果如下
[root@VM-34-63-centos mydockerfile]# docker run ce
.
..
.dockerenv
bin
boot
dev # 以下省略
```



# 示例

```dockerfile
# 创建 dockerfile目录并进入然后编写自己的dockerfile
mkdir dockerfile && cd dockerfile && vim mydockerfile

# 编写 mydockerfile
FROM centos:latest     
MAINTAINER 姚留洋 762301880@qq.com

ENV MYPATH /usr/local # 设置环境变量工作的目录
WORKDIR	$MYPATH   # 取上面设置的便利的名称

RUN yum -y install vim
RUN yum -y install net-tools 

EXPOSE 80

CMD echo $MYPATH
CMD echo "--构建完毕--"
CMD /bin/bash

# 构建镜像
# -f 指定镜像的路径  -t(tagged) 镜像名:版本号
docker build -f mydockerfile -t mycentos:0.1 . # 出现Successfully built 镜像id 及构建镜像成功

# 列出构建的镜像
[root@VM-30-101-centos ~]# docker images
REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
mycentos            0.1                 90ff9ad7dee5        5 minutes ago       304 MB
docker.io/centos    latest              5d0da3dc9764        4 weeks ago         231 MB

# 启动镜像
docker run -itd mycentos:0.1
```



## 创建一个dockerfiledocker run -itd mycentos:0.1

>1、下面以定制一个 nginx 镜像（构建好的镜像内会有一个 /usr/share/nginx/html/index.html 文件）
>
>在一个空目录下，新建一个名为 Dockerfile 文件，并在文件内添加以下内容：

```shell
mkdir mynginx
cd mynginx
touch Dockerfile     # 名字可以随机,建议Dockerfile
vim Dockerfile  # 编辑Dockerfile


# 文件中的内容 其中所有的指令都是大写(参数)的，这里每个指令就是镜像的一层

FROM nginx:latest
RUN echo '这是一个本地构建的nginx镜像' > /usr/share/nginx/html/index.html
```

## 构建一个基本镜像

- 在docker目录中打开终端执行命令

``` 
# 构建镜像
docker build -f   Fockerfile绝对路径 .
```





## 常用命令



### ADD

> ADD复制本地主机文件、目录或者远程文件 URLS 从 并且添加到容器指定路径中 。

```shell
ADD <src>... <dest>
```

ADD复制本地主机文件、目录或者远程文件 URLS 从 并且添加到容器指定路径中 。

支持通过 [Go](http://lib.csdn.net/base/go) 的正则模糊匹配，具体规则可参见 [Go filepath.Match](http://golang.org/pkg/path/filepath/#Match)

```
ADD hom* /mydir/        # adds all files starting with "hom"
ADD hom?.txt /mydir/    # ? is replaced with any single character
```

- 路径必须是绝对路径，如果 不存在，会自动创建对应目录
- 路径必须是 Dockerfile 所在路径的相对路径
- 如果是一个目录，只会复制目录下的内容，而目录本身则不会被复制



### COPY

> COPY复制新文件或者目录从 并且添加到容器指定路径中

```shell
COPY a.txt /a.txt 
# 此命令将Dockerfile中同级的文件复制到容器内部中	
# 用法同ADD，唯一的不同是不能指定远程文件 URLS。 
```





# 实战

## Tomcat镜像

1、准备镜像文件 tomcat 压缩包，jdk(java开发环境)的压缩包

**资料**

| **地址**                | **链接**                                                     |
| ----------------------- | ------------------------------------------------------------ |
| tomcat官网，tomcat9下载 | [link](https://tomcat.apache.org/)          [link](https://tomcat.apache.org/download-90.cgi) |
| jdk官网,jdk8下载        | [link](https://www.oracle.com/java/technologies/downloads/)          [link](http://jdk.java.net/java-se-ri/8-MR3) |

**准备环境**

```shell
# 创建tomcat 镜像文件夹，创建Dockerfile文件 下载 tomcat 压缩包,下载jdk工具包
# 推荐 tomcat 压缩包，jdk工具包源码文件下载到mytomcat文件夹中

mkdir mytomcat && cd mytomcat &touch Dockerfil && wget https://dlcdn.apache.org/tomcat/tomcat-9/v9.0.54/bin/apache-tomcat-9.0.54.tar.gz && wget https://download.java.net/openjdk/jdk8u41/ri/openjdk-8u41-b04-linux-x64-14_jan_2020.tar.gz
```

**编写**[^Dockerfile]文件

> 官方命名[^Dockerfile], ***build***会自动寻找这个文件，就不需要**-f**指定了！

```dockerfile
# 选择镜像
FROM centos:latest
# 设置作者信息
MAINTAINER yaoliuyang<762301880@qq.com>
# ADD 命令会自动解压压缩文件 把tomcat&jdk放到想要的目录
ADD apache-tomcat-9.0.54.tar.gz  /usr/local/
ADD openjdk-8u41-b04-linux-x64-14_jan_2020.tar.gz /usr/local/
# 安装所需要的软件
RUN yum -y install vim
# 设置环境变量
ENV MYPATH /usr/local
# 设置工作目录 变量是上面设置的环境变量
WORK $MYPATH
# 设置java环境变量
ENV JAVA_HOME /usr/local/openjdk-8u41-b04
ENV CLASSPATH  $JAVA_HOME/lib/dt.jar;$JAVA_HOME/lib/tools.jar
# 配置tomcat 目录
ENV CATALINA_HOME /usr/local/apache-tomcat-9.0.54
ENV CATALINA_BASH /usr/local/apache-tomcat-9.0.54
# 保存path
ENV PATH $PATH;$JAVA_HOME/bin;$CATALINA_HOME/lib;$CATALINA_HOME/bin
# 暴露端口
EXPOSE 8080
# 启动并输出日志
CMD /usr/local/apache-tomcat-9.0.54/bin/startup.sh && tail -F /usr/local/apache-tomcat-9.0.54/bin/logs/catalina.out
```

- 构建[^dockerfile]

```shell
docker build -t mytomcat:0.1 .
```

- 启动镜像

```php
docker run -itd -p 9090:8080 --name mytomcat -v /本地tomcat配置:容器内部配置(/usr/local/apache-tomcat-9.0.54/webapps/外部配置) 容器id 
```







