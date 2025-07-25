

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
> 5. [Drone](https://www.drone.io/)
> 6. [Semaphore CI](https://link.jianshu.com/?t=https%3A%2F%2Fsemaphoreci.com%2F)
> 7. [Buildkite](https://link.jianshu.com/?t=https%3A%2F%2Fbuildkite.com%2F)
> 8. [Wercker](https://link.jianshu.com/?t=http%3A%2F%2Fwww.wercker.com%2F)
> 10. [TeamCity](https://link.jianshu.com/?t=https%3A%2F%2Fwww.jetbrains.com%2Fteamcity%2F)

## 资料

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| jenkins官网        | [link](https://www.jenkins.io/zh/)                           |
| jenkins-docker官网 | [link](https://registry.hub.docker.com/_/jenkins)            |
| 参考博客           | [link](https://learnku.com/articles/39601?spm=a2c6h.12873639.0.0.6a06b3069048Mh)  [link](https://blog.51cto.com/u_8416177/2129777)  [link](https://blog.51cto.com/bigboss/2129477)  [link](https://blog.csdn.net/zqqiang0307/article/details/120458586) |

#  [安装jenkins](https://developer.aliyun.com/article/742451)

## **docker安装jenkins**

> 可以官网下载更多版本 https://hub.docker.com/r/jenkins/jenkins/tags
>
> 这里我找了个可以用的版本因为2.60.3 版本的死活缺少插件装了也没有，最新版本的安装gitlab 扩展提示jdk版本过高不支持

```shell
# 指定jdkb
docker pull jenkins/jenkins:latest-jdk8 

# 安装最新版本
docker pull jenkins/jenkins:latest
```

###  补充:

#### 如果不小心忘记admin登陆密码怎么办

Jenkins 初始管理员（admin）密码的获取方式取决于部署方式，以下是具体方法：

1. 用 Docker 部署的 Jenkins（含 docker-compose）

通过容器日志或数据卷文件获取：

方法一：查看容器日志（推荐）

```bash
# 查看 Jenkins 容器日志，初始密码会打印在日志中
docker logs jenkins
```

在日志中搜索 `initialAdminPassword`，会看到类似这样的内容：

```shell
*************************************************************
*************************************************************
*************************************************************

Jenkins initial setup is required. An admin user has been created and a password generated.
Please use the following password to proceed to installation:

abcdef1234567890  # 这就是初始密码

This may also be found at: /var/jenkins_home/secrets/initialAdminPassword

*************************************************************
```





## **启动jenkins镜像**

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

![1636450936(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/xAjnCo9B8XZOtKN.png)

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

![1636452627(1).png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/JiTSRpWZDye6LAs.png)

**发生以下报错解决方案**(这里可以不用看了不是2.60.3版本就行)

>安装过程中出现错误：没有这样的插件：cloudbees-folder

![zGyWpDOHJ83xM6E](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/zGyWpDOHJ83xM6E.png)

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

![1640570777(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/9yaLbzjECZteSKk.png)

## **添加服务器地址密码**

> 调试日志 **/var/log/secure** 记录用户登录日志

**下载ssh插件 SSH plugin**

![image-20211229152002472](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152002472.png)

**配置ssh**

![image-20211229151814397](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229151814397.png)

**第一种添加服务器账户&密码的方法**

> 这种方式是可以但是不是我想要的所以做保留**类型为:**<font color='red'>Username with password</font>

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220726105101549.png" alt="image-20220726105101549" style="zoom:50%;" />

## 新建任务

### **新建任务名称**

![image-20211229152239564](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152239564.png)

### 添加git 信息

> 可以直接选择用户名&密码的形式(**不推荐这样使用**)

![image-20211229152416670](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152416670.png)

#### 第二种方式(token方式)

> 用户名密码不是很安全建议采用apitoken形式，请在**jenkins下载 GitLab Plugin**插件、

**下载插件**

![1rvQJBXhYewOAiG](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1rvQJBXhYewOAiG.png)

**创建gitlab访问 令牌**

> 去**gitlab**点击右上角的**头像**-**Preferences**-**Access Tokens**

![img](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/Q42xTme8IvCEnDL.jpeg)

**创建后返回的访问令牌**

> 这里记得自己复制一下令牌，别搞丢了就行

![1648198799(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/ayRl1LItD3TbqMi.png)

**配置gitlab链接**

![fLek9lEnqjUbO3P](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/fLek9lEnqjUbO3P.png)

**流水线配置**

> 选择自己的gitlab配置

![1648203176(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/QGLF7KdyhisSEav.png)

> 记住对应的源码管理要关闭

![5a2QU4xjEfg6mLV](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/5a2QU4xjEfg6mLV.png)

### 添加构建

![image-20211229152524274](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152524274.png)

### **添加钩子**

![image-20211229152635739](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152635739.png)

**配置gitlab钩子**

![image-20211229152754135](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152754135.png)

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

![image-20211229152933074](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20211229152933074.png)



# 补充

## 关于升级jenkins

> 可以在清华大学镜像站下载war包复制到替换容器中的以下目录

```shell
/usr/share/jenkins/jenkins.war
```



## 凭据管理<font color='yellow'>SSH Username with private key</font>

> **说明**
>
> 关于这个为什么我一定要单独记录一下,因为这个我踩坑好几天了，一直以为这个ssh是用来远程连接
>
> 服务器使用的,最后发现是用来连接**git仓库拉取拉取代**码用的

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://javalib.blog.csdn.net/article/details/124416957?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124416957-blog-111944479.pc_relevant_multi_platform_whitelistv2_exp180w&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-124416957-blog-111944479.pc_relevant_multi_platform_whitelistv2_exp180w&utm_relevant_index=1) [link](http://t.zoukankan.com/faithH-p-14949070.html) |

###  示例

**添加ssh凭证**

> SSH Username with password：SSH 账号和密钥，免密登录的方式。
>
> 描述:添加表述内容
>
> username:一般是gitlab账号邮箱
>
> privateKey:是同上传到**git仓库上的私钥** 这里的私钥不是文件形式是以**-----BEGIN OPENSSH PRIVATE KEY-----  开始     -----END OPENSSH PRIVATE KEY----- 结束** 的全部字符（ **cat ~/.ssh/id_rsa** 命令查询私钥复制即可） 

![image-20220726133817478](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220726133817478.png)

**使用**

> 源码管理种使用
>
> **Credentials**证书,配置即可连接git

![image-20220726134418036](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220726134418036.png)

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

![1650936648(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/4i32EamkISozdf6.png)

![image-20220214112340916](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220214112340916.png)



**找到  系统管理-全局安全配置**

![image-20220214112058200](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220214112058200.png)

**放开授权策略**

![image-20220214112203568](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220214112203568.png)
