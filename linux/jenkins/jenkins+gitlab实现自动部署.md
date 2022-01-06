#  说明

> 本人已经玩过jenkins了只不过那时候一知半解的实现了自动构建
>
> 所以这次打算一点点刨析,这里我们采用docker安装jenkins
>
> ## 更多ci工具备注
>
> 1. [Travis CI](https://link.jianshu.com/?t=https%3A%2F%2Ftravis-ci.org%2F)
> 2. [Circle CI](https://link.jianshu.com/?t=https%3A%2F%2Fcircleci.com%2F)
> 3. [Jenkins](https://link.jianshu.com/?t=https%3A%2F%2Fjenkins.io%2F)
> 4. [AppVeyor](https://link.jianshu.com/?t=https%3A%2F%2Fwww.appveyor.com%2F)
> 5. [CodeShip](https://link.jianshu.com/?t=https%3A%2F%2Fcodeship.com%2F)
> 6. [Drone](https://link.jianshu.com/?t=http%3A%2F%2Ftry.drone.io%2F)
> 7. [Semaphore CI](https://link.jianshu.com/?t=https%3A%2F%2Fsemaphoreci.com%2F)
> 8. [Buildkite](https://link.jianshu.com/?t=https%3A%2F%2Fbuildkite.com%2F)
> 9. [Wercker](https://link.jianshu.com/?t=http%3A%2F%2Fwww.wercker.com%2F)
> 10. [TeamCity](https://link.jianshu.com/?t=https%3A%2F%2Fwww.jetbrains.com%2Fteamcity%2F)

## 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| jenkins-docker官网 | [link](https://registry.hub.docker.com/_/jenkins)            |
| 参考博客           | [link](https://learnku.com/articles/39601?spm=a2c6h.12873639.0.0.6a06b3069048Mh)  [link](https://blog.51cto.com/u_8416177/2129777) |

#  [安装](https://developer.aliyun.com/article/742451)

**安装jenkins**

> 可以官网下载更多版本 https://hub.docker.com/r/jenkins/jenkins/tags
>
> 这里我找了个可以用的版本因为2.60.3 版本的死活缺少插件装了也没有，最新版本的安装gitlab 扩展提示jdk版本过高不支持

```shell
docker pull jenkins/jenkins:latest-jdk8
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

**发生以下报错解决方案**(这里可以不用看了不是2.60.3版本就行)

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

# 配置

## 配置gitlab信息

> 这里记住下载gitlab的插件

![1640570777(1).jpg](https://s2.loli.net/2021/12/27/9yaLbzjECZteSKk.png)

**添加服务器地址密码**

- 下载ssh插件 第一次下载应该有一个叫做ssh

![image-20211229152002472](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152002472.png)

- 配置ssh

![image-20211229151814397](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229151814397.png)

## 新建任务

![image-20211229152239564](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152239564.png)

![image-20211229152416670](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152416670.png)

![image-20211229152524274](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152524274.png)

**添加钩子**

![image-20211229152635739](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152635739.png)

**添加钩子**

![image-20211229152754135](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152754135.png)

[**脚本注意**](https://yuandongming.blog.csdn.net/article/details/104505624?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&utm_relevant_index=2)

> 这里要加上`git stash` 问了防止万一不想等自动构建然后直接在服务器上改代码的情况下直接拉取代码会冲突
>
> 所以需要封存起现有的代码然后再拉取服务器代码
>
> **不推荐使用 `git reset --hard` 这条命令会将现有代码也会保存在服务器上**

```shell
cd /data/work/laravel_study &&  git stash && git pull
```

![image-20211229152933074](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152933074.png)



# 补充

## 关于升级jenkins

> 可以在清华大学镜像站下载war包复制到替换容器中的以下目录

```shell
/usr/share/jenkins/jenkins.war
```

