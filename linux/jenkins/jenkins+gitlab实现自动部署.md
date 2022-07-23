

#  说明

> 本人已经玩过jenkins了只不过那时候一知半解的实现了自动构建
>
> 所以这次打算一点点刨析,这里我们采用docker安装jenkins
>
> ## 更多ci工具备注
>
> 1. [Travis CI](https://link.jianshu.com/?t=https%3A%2F%2Ftravis-ci.org%2F)
> 2. [Circle CI](https://link.jianshu.com/?t=https%3A%2F%2Fcircleci.com%2F)
> 3. [AppVeyor](https://link.jianshu.com/?t=https%3A%2F%2Fwww.appveyor.com%2F)
> 4. [CodeShip](https://link.jianshu.com/?t=https%3A%2F%2Fcodeship.com%2F)
> 5. [Drone](https://link.jianshu.com/?t=http%3A%2F%2Ftry.drone.io%2F)
> 6. [Semaphore CI](https://link.jianshu.com/?t=https%3A%2F%2Fsemaphoreci.com%2F)
> 7. [Buildkite](https://link.jianshu.com/?t=https%3A%2F%2Fbuildkite.com%2F)
> 8. [Wercker](https://link.jianshu.com/?t=http%3A%2F%2Fwww.wercker.com%2F)
> 10. [TeamCity](https://link.jianshu.com/?t=https%3A%2F%2Fwww.jetbrains.com%2Fteamcity%2F)

## 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| jenkins官网        | [link](https://www.jenkins.io/zh/)                           |
| jenkins-docker官网 | [link](https://registry.hub.docker.com/_/jenkins)            |
| 参考博客           | [link](https://learnku.com/articles/39601?spm=a2c6h.12873639.0.0.6a06b3069048Mh)  [link](https://blog.51cto.com/u_8416177/2129777)  [link](https://blog.51cto.com/bigboss/2129477) |

#  [安装](https://developer.aliyun.com/article/742451)

**安装jenkins**

> 可以官网下载更多版本 https://hub.docker.com/r/jenkins/jenkins/tags
>
> 这里我找了个可以用的版本因为2.60.3 版本的死活缺少插件装了也没有，最新版本的安装gitlab 扩展提示jdk版本过高不支持

```shell
# 指定jdkb
docker pull jenkins/jenkins:latest-jdk8 

# 安装最新版本
docker pull jenkins/jenkins:latest
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
docker run  -itd -p 8080:8080 -p 50000:50000 --restart=always  --name jenkins  -v /var/jenkins:/var/jenkins_home 镜像id
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


# 或者直接外部查看密码
 docker exec -it jenkins(容器名称)  cat /var/jenkins_home/secrets/initialAdminPassword
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

## **添加服务器地址密码**

**下载ssh插件 SSH plugin**

![image-20211229152002472](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152002472.png)

**配置ssh**

![image-20211229151814397](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229151814397.png)

## 新建任务

### **新建任务名称**

![image-20211229152239564](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152239564.png)

### 添加git 信息

> 可以直接选择用户名&密码的形式(**不推荐这样使用**)

![image-20211229152416670](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152416670.png)

#### 第二种方式(token方式)

> 用户名密码不是很安全建议采用apitoken形式，请在**jenkins下载 GitLab Plugin**插件、

**下载插件**

![1648198573(1).jpg](https://s2.loli.net/2022/03/25/1rvQJBXhYewOAiG.png)

**创建gitlab访问 令牌**

> 去**gitlab**点击右上角的**头像**-**Preferences**-**Access Tokens**

![1648198709(1).jpg](https://s2.loli.net/2022/03/25/Q42xTme8IvCEnDL.png)

**创建后返回的访问令牌**

> 这里记得自己复制一下令牌，别搞丢了就行

![1648198799(1).jpg](https://s2.loli.net/2022/03/25/ayRl1LItD3TbqMi.png)

**配置gitlab链接**

![1648203061(1).jpg](https://s2.loli.net/2022/03/25/fLek9lEnqjUbO3P.png)

**流水线配置**

> 选择自己的gitlab配置

![1648203176(1).jpg](https://s2.loli.net/2022/03/25/QGLF7KdyhisSEav.png)

> 记住对应的源码管理要关闭

![1648203248(1).jpg](https://s2.loli.net/2022/03/25/5a2QU4xjEfg6mLV.png)

### 添加构建

![image-20211229152524274](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152524274.png)

### **添加钩子**

![image-20211229152635739](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152635739.png)

**配置gitlab钩子**

![image-20211229152754135](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152754135.png)

[**脚本注意**](https://yuandongming.blog.csdn.net/article/details/104505624?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1.no_search_link&utm_relevant_index=2)

> 这里要加上`git stash` 问了防止万一不想等自动构建然后直接在服务器上改代码的情况下直接拉取代码会冲突
>
> 所以需要封存起现有的代码然后再拉取服务器代码 
>
> `git stash clear` 清除所有封存堆栈中的所有内容
>
> **不推荐使用 `git reset --hard` 这条命令会将现有代码也会保存在服务器上**

```shell
cd /data/work/laravel_study && git clean -f &&  git stash && git pull && git stash clear
```

![image-20211229152933074](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20211229152933074.png)



# 补充

## 关于升级jenkins

> 可以在清华大学镜像站下载war包复制到替换容器中的以下目录

```shell
/usr/share/jenkins/jenkins.war
```





# bug 解析

**构建遇到:he following untracked working tree files would be overwritten by merg**

> error: The following untracked working tree files would be overwritten by merge:
> config/required_remote_url.php
> Please move or remove them before you can merge.
>
> 错误:以下未跟踪的工作树文件将被合并覆盖:    配置/ required_remote_url.php   请在合并之前移动或删除它们。

> bug复现   加入你本地创建了一个a文件  你先没有通过git 提交自动构建 直接 通过shell 等工具上传到远程服务器
>
> 会照成git没有这个文件的记录所以会报这种错误
>
> 我们只需要删除超前的文件即可

```shell
git clean -n
// 是一次 clean 的演习, 告诉你哪些文件会被删除，不会真的删除
 
git clean -f
// 删除当前目录下所有没有 track 过的文件 f：强制运行
// 不会删除 .gitignore 文件里面指定的文件夹和文件, 不管这些文件有没有被 track 过
 
git clean -f <path>
// 删除指定路径下的没有被 track 过的文件
 
git clean -df
 
// 删除当前目录下没有被 track 过的文件和文件夹
 
git clean -xf
 
// 删除当前目录下所有没有 track 过的文件.
// 不管是否是 .gitignore 文件里面指定的文件夹和文件
 
git clean 
// 对于刚编译过的项目也非常有用
// 如, 他能轻易删除掉编译后生成的 .o 和 .exe 等文件. 这个在打包要发布一个 release 的时候非常有用
```

**推荐构建脚本加上git clean -df 命令**

> 记住这个命令**千万不要再本地使用会导致误删很多东西**

```shell
git clean -f
    
# 例
cd /data/work/laravel_study && git clean -f &&  git stash && git pull && git stash clear       
```

## Hook 成功执行但返回 HTTP 403 <html> <head> <meta http-equiv="Content-Type" 

> jenkins配置完毕但是再怎么推送都无法自动构建,怀疑是钩子的问题然后测试了一下发现无法测试成功，正常因该是200
>
> 如下图所示测试jenkins钩子的时候发生403错误

![1650936648(1).jpg](https://s2.loli.net/2022/04/26/4i32EamkISozdf6.png)

![image-20220214112340916](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220214112340916.png)



**找到  系统管理-全局安全配置**

![image-20220214112058200](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220214112058200.png)

**放开授权策略**

![image-20220214112203568](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220214112203568.png)
