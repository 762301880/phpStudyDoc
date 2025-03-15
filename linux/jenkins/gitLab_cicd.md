## 资料

| 名称                | 地址                                   |
| ------------------- | -------------------------------------- |
| gitlab-cicd文档地址 | [link](](https://docs.gitlab.com/ci/)) |



## 简单示例一

###  逻辑

要实现自动将GitLab仓库中的代码推送到远程服务器，可以使用GitLab的CI/CD功能。下面是一些步骤：

1. 在远程服务器上设置好SSH密钥，以便GitLab能够访问远程仓库并自动推送代码。
2. 在GitLab项目中，创建一个.gitlab-ci.yml文件，并配置需要执行的CI流程。这里，我们可以使用rsync命令将代码推送到远程服务器。以下是一个简单的示例：

```shell
# 定义使用的Docker镜像
image: alpine:latest
# 定义工作流的阶段
stages:
  - deploy
# 部署阶段
deploy:
  # 指定阶段名称
  stage: deploy
  # 定义部署前的准备工作
  before_script:
    - echo $REMOTE_DIRECTORY  # 显示操作目录
    - echo $SSH_USER # 显示用户名
    - echo $SSH_HOST # 显示ip地址
    - apk add openssh
    - apk add sshpass
    - which sshpass
  # 定义部署任务
  script:
    - sshpass -p "$SSH_PASSWORD" ssh -o StrictHostKeyChecking=no $SSH_USER@$SSH_HOST "cd $REMOTE_DIRECTORY && git pull origin develop"
  # 指定仅针对 master 分支部署代码
  only:
    - develop
```

### 知识点补充

#### sshpass命令详解

> **场景**
>
> 当您使用ssh命令时，它会提示您输入用户名和密码。但是，如果您想使用自动化脚本或批处理文件连接到远程主机，则这种行为会显得很麻烦，并需要人工输入密码。在这种情况下，sshpass工具就可以派上用场。
>
> SSHpass是一个使用密码自动进行SSH连接的非常简单的实用程序。简单来说，它从调用方接收SSH选项和命令，然后将密码传递到ssh客户端。
>
> 使用sshpass的优点是不需要手动输入密码，让人类用户变得简单。但是缺点也很明显，安全性。由于SSH传输的不是加密文本而是明文密码，因此会将密码泄露到网络上。
>
> **语法介绍**
>
> sshpass是一个命令行工具，用于自动化通过SSH连接进行认证的过程。它可以避免在远程服务器上手动输入密码或交互式地输入密码。此工具使用了一种非常简单的方法，只需将密码直接传递给SSH客户端，从而允许自动化SSH连接。sshpass的语法为：
>
> ```shell
> sshpass [-f|-d|-P|-e] [-hV] ssh_options command
> # 例子
> sshpass -p password ssh user@hostname
> ```
>
> 其中，-p选项后面的是您的密码。请注意，在生产环境中，此选项提供的明文密码可能会成为潜在的安全风险，因此请使用其他更安全的方法，例如使用SSH密钥对进行身份验证。ssh_options是SSH命令的默认参数，例如远程主机地址、端口、用户名等。sshpass命令后面的command参数是要在远程主机上运行的任何命令或脚本。sshpass支持以下几个选项：
>
> - -f：从文件中读取密码；
> - -d：从环境变量中读取密码；
> - -P：使用管道传送密码；
> - -e：使用了转义字符。
>
> **缺点**
>
> sshpass是一种方便快捷的工具，它为管理员提供了轻松自动化SSH会话的方法。但是，请注意，它的使用风险随处皆是。如果您决定使用该工具，请确保您理解其实用性和相应的安全问题。
>
> 需要注意的是，sshpass对于安全性来说是不太理想的，因为它需要明文传输密码。因此，只有在安全性不是很重要的情况下才应该使用sshpass。在生产环境中，推荐使用SSH密钥对进行身份验证，以提高系统的安全性。

####  自动更新swoole

> **exec 不能加-it** 
>
> 这个错误提示表明你正在尝试在非交互设备上执行一个需要终端交互的命令。在你的命令中，`-it`选项指示`docker exec`命令需要一个交互式终端会话。
>
> 然而，由于你正在通过SSH连接执行命令，而SSH连接不会提供实际的TTY终端，所以会报错。要解决这个问题，你可以尝试去掉`-it`选项，将`docker exec`命令改为非交互式执行。
>
> 这样，你可以通过SSH连接执行`docker exec`命令，并列出`php7.4-fpm`容器中的文件列表。记住，由于没有使用交互式终端，可能需要适当调整命令以适应非交互式执行，并且某些命令的行为可能会有所不同。

```shell
  # 定义部署任务
  script:
    - sshpass -p "$SSH_PASSWORD" ssh -o StrictHostKeyChecking=no $SSH_USER@$SSH_HOST "cd $REMOTE_DIRECTORY && git pull origin develop && docker exec php7.4-fpm supervisorctl restart all"
```



### **对应变量配置**

> 需要注意的是编辑变量勾选的时候不要点击了
>
> **Protect variable**  选项导致无法使用变量

![image-20230520094847798](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20230520094847798.png)

这个示例中，脚本会将GitLab仓库中的代码同步到远程服务器中的项目路径。其中，$CI_PROJECT_DIR代表GitLab CI在运行时克隆的项目目录，user和remote_server需要替换为实际的用户名和远程服务器的IP地址。

1. 提交代码到GitLab，然后等待CI流程自动执行。一旦代码合并到master分支，CI流程就会开始自动执行，其中deploy阶段会执行rsync命令将代码推送到远程服务器。

这样，每次将代码合并到master分支时，就会自动将代码推送到远程服务器，实现自动化部署。需要注意的是，这种部署方式需要在远程服务器上设置好SSH密钥，并注意保护好SSH密钥的安全。

### bug解析

#### [$ sshpass -p "$SSH_PASSWORD" ssh -o StrictHostKeyChecking=no $SSH_USER@$SSH_HOST "cd $REMOTE_DIRECTORY && git pull origin develop" SSHPASS: Failed to run command: No such file or directory](https://www.thinbug.com/q/21375125)

> **上述情况请检查sshpass是否安装好且ssh是否存在**,本人是因为**ssh**没有安装



#### Warning: Permanently added '60.204.148.255' (ED25519) to the list of known hosts. Permission denied, please try again.

> 这种情况就是密码没有设置好,看看变量那边的密码是否被保护了设置错了实在不行就在配置文件里面写死去测试

#### SSHPASS: Failed to run command: No such file or directory

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/weixin_45536921/article/details/115630227) |

报错请安装**openssh-client**

```shell
/code # apk add openssh-client
(1/4) Installing openssh-keygen (8.8_p1-r1)
(2/4) Installing libedit (20210910.3.1-r0)
(3/4) Installing openssh-client-common (8.8_p1-r1)
(4/4) Installing openssh-client-default (8.8_p1-r1)
Executing busybox-1.34.1-r3.trigger
OK: 143 MiB in 53 packages
```

