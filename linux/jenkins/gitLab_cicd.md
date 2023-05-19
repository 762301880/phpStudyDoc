要实现自动将GitLab仓库中的代码推送到远程服务器，可以使用GitLab的CI/CD功能。下面是一些步骤：

1. 在远程服务器上设置好SSH密钥，以便GitLab能够访问远程仓库并自动推送代码。
2. 在GitLab项目中，创建一个.gitlab-ci.yml文件，并配置需要执行的CI流程。这里，我们可以使用rsync命令将代码推送到远程服务器。以下是一个简单的示例：

```shell
stages:
  - deploy

deploy:
  stage: deploy
  script:
    - rsync -avz --delete $CI_PROJECT_DIR/ user@remote_server:/path/to/project/
  only:
    - master
```



这个示例中，脚本会将GitLab仓库中的代码同步到远程服务器中的项目路径。其中，$CI_PROJECT_DIR代表GitLab CI在运行时克隆的项目目录，user和remote_server需要替换为实际的用户名和远程服务器的IP地址。

1. 提交代码到GitLab，然后等待CI流程自动执行。一旦代码合并到master分支，CI流程就会开始自动执行，其中deploy阶段会执行rsync命令将代码推送到远程服务器。

这样，每次将代码合并到master分支时，就会自动将代码推送到远程服务器，实现自动化部署。需要注意的是，这种部署方式需要在远程服务器上设置好SSH密钥，并注意保护好SSH密钥的安全。