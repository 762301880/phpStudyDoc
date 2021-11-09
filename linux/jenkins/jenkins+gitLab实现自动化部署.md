#  说明

> 本人已经玩过jenkins了只不过那时候一知半解的实现了自动构建
>
> 所以这次打算一点点刨析,这里我们采用docker安装jenkins

## 资料

| 名称               | 地址                                              |
| ------------------ | ------------------------------------------------- |
| jenkins-docker官网 | [link](https://registry.hub.docker.com/_/jenkins) |

#  安装

**安装jenkins**

```shell
docker pull jenkins
```

**启动jenkins镜像**

```shell
docker run  -itd -p 8080:8080 -p --name jenkins 50000:50000 -v /your/home:/var/jenkins_home 镜像id
```

**打开jenkins**

> 安装完成之后需要进入容器内部找到此路径下的密码输入到下面的文本框中

![1636450936(1).jpg](https://i.loli.net/2021/11/09/xAjnCo9B8XZOtKN.png)

```shell
# 查看容器
docker ps -a
# 进入容器内部
docker exec -it  容器id /bin/bash
# 查看密码
jenkins@37b124ebf446:/$ cat /var/jenkins_home/secrets/initialAdminPassword
11ac1d04bf5d4932a85c1d7788cc76b7
```

