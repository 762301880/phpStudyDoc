## 简单示例一

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

### **对应变量配置**

> 需要注意的是编辑变量勾选的时候不要点击了
>
> **Protect variable**  选项导致无法使用变量

![image-20230520094847798](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20230520094847798.png)

这个示例中，脚本会将GitLab仓库中的代码同步到远程服务器中的项目路径。其中，$CI_PROJECT_DIR代表GitLab CI在运行时克隆的项目目录，user和remote_server需要替换为实际的用户名和远程服务器的IP地址。

1. 提交代码到GitLab，然后等待CI流程自动执行。一旦代码合并到master分支，CI流程就会开始自动执行，其中deploy阶段会执行rsync命令将代码推送到远程服务器。

这样，每次将代码合并到master分支时，就会自动将代码推送到远程服务器，实现自动化部署。需要注意的是，这种部署方式需要在远程服务器上设置好SSH密钥，并注意保护好SSH密钥的安全。

### bug解析

#### [$ sshpass -p "$SSH_PASSWORD" ssh -o StrictHostKeyChecking=no $SSH_USER@$SSH_HOST "cd $REMOTE_DIRECTORY && git pull origin develop" SSHPASS: Failed to run command: No such file or directory](https://www.thinbug.com/q/21375125)

> **上述情况请检查sshpass是否安装好且ssh是否存在**,本人是因为**ssh**没有安装



#### Warning: Permanently added '60.204.148.255' (ED25519) to the list of known hosts. Permission denied, please try again.

> 这种情况就是密码没有设置好,看看变量那边的密码是否被保护了设置错了实在不行就在配置文件里面写死去测试