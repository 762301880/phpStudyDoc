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

> 将配置文件、编译日志、结果归档都存储在 *JENKINS_HOME* *目录*挂载到本机目录
>
> $ id
> uid=1000(jenkins) gid=1000(jenkins) groups=1000(jenkins)
>
> 本地拥有的是root权限的目录,容器中 的目录uid为1000

```shell
mkdir /var/jenkins && chown -R 1000 /var/jenkins/ # 这一步不执行端口都无法显示出来
docker run  -itd -p 8080:8080 -p 50000:50000 --name jenkins	  -v /var/jenkins:/var/jenkins_home 镜像id
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

**安装建议的插件**

![1636452627(1).png](https://i.loli.net/2021/11/09/JiTSRpWZDye6LAs.png)

**发生以下报错解决方案**

>安装过程中出现错误：没有这样的插件：cloudbees-folder

![截图_选择区域_20211225141102.png](https://s2.loli.net/2021/12/25/zGyWpDOHJ83xM6E.png)

```shell
# https://updates.jenkins-ci.org/download/plugins/cloudbees-folder/下载cloudbees-folder 插件
# --no-check-certificate不校验证书	
# 在宿主机的/var/jenkins_home下找到war/WEB-INF/detached-plugins这个目录，添加这个插件，然后重新启动jenkins容器

cd /var/jenkins_home/war/WEB-INF/detached-plugins
# 下载地址 https://mirrors.tuna.tsinghua.edu.cn/jenkins/plugins/cloudbees-folder/
wget https://updates.jenkins-ci.org/download/plugins/cloudbees-folder/6.17/cloudbees-folder.hpi --no-check-certificate

# 然后退出容器并刷新容器
exit
docker restart 容器id
```

