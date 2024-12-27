# 常用命令

## git 删除分支

### 删除本地分支

```shell
git branch -d 分支名

# 如果要强制删除本地分支，即使它尚未完全合并到其他分支，可以使用以下命令：
git branch -D 分支名
```

### 删除远程分支    

> 这将从远程仓库中删除指定的分支。请确保在执行此操作之前已经确认了该分支不再需要，并且所有相关的更改都已经合并到其他分支或被丢弃。

```shell
git push origin --delete 分支名
```

## [git 生产ssh密钥](https://www.cnblogs.com/yaoliuyang/p/13060779.html)

1.首先配置用户名与邮箱

> 此配置会在本地用户目录下生成`.gitconfig`文件
* 用户名的配置：
```
git config --global user.name "yaoliuyang"
```
* 邮箱的配置
```
 git config --global user.email "762301880@qq.com"
```
<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200607153638000-1303370550.png' width='600px' heigth='400px' title='示例'>

2.生成ssh密匙：

> ssh-keygen: 是一个用于创建 SSH 密钥对的工具。
> -t rsa: 指定生成的密钥类型为 RSA。RSA 是一种非对称加密算法，常用于 SSH 认证。
> -C "您的邮箱@qq.com": 为生成的密钥添加一个注释，通常用于标识密钥的所有者或用途。这里的注释是 "您的邮箱@qq.com"。
>
> 执行这条命令后，会在用户的 ~/.ssh 目录下生成两个文件：
> id_rsa: 私钥文件，需要妥善保管，不要泄露。
> id_rsa.pub: 公钥文件，可以安全地分享给需要进行 SSH 认证的服务器。
> 生成的密钥对可以用于无密码登录远程服务器，提高工作效率

```
ssh-keygen -t rsa -C "您的邮箱@qq.com"
```
*连续点击三次`回车`会在用户目录下生成==.ssh==目录
>找到其中的`id_rsa`用记事本打开复制里面的秘钥内容
>*用命令的方式打开秘钥(复制即可)：
```
 cat ~/.ssh/id_rsa.pub
```
<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200607154032405-1871716298.png' width='600px' heigth='400px' title='示例'>

在代码库中添加公匙

<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200607154148138-609470938.png' width='600px' heigth='400px' title='示例'>

##  git 提交代码忽略文件或目录

**资料**

| 名称                      | 地址                                                         |
| ------------------------- | ------------------------------------------------------------ |
| 官方文档                  | [link](https://git-scm.com/docs/gitignore)                   |
| 菜鸟教程`.gitignore` 说明 | [link](https://www.runoob.com/w3cnote/android-tutorial-git-repo-operate.html) |

在使用 Git 提交代码时，如果希望忽略某些文件或目录，可以通过以下几种方法实现：

- `.gitignore` 文件：
  1. 在项目根目录下创建或编辑` .gitignore` 文件。
  2. 在 `.gitignore` 文件中添加需要忽略的文件或目录路径。例如：

```shell
    # 忽略所有 .log 文件
    *.log

    # 忽略 build 目录
    /build/

    # 忽略特定文件
    config.php
```

- 全局 `.gitignore `文件：
  1. 如果有一些文件是你在多个项目中都不希望被跟踪的，可以设置一个全局的` .gitignore `文件。
  2. 使用以下命令设置全局` .gitignore `文件：

```shell
git config --global core.excludesfile ~/.gitignore_global
```





#  实战

##  git 同时提交到两个地址 例如gitlab与gitcode

**定义多个远程仓库：分别为每个 Git 服务器定义一个远程仓库名称，比如 origin 和 mirror 或其他你喜欢的名字**

```shell
  git remote add origin git@gitlab.com:yly_python/py_demo.git
  git remote add mirror git@gitcode.com:yaoliuyang/py_demo.git
  
  
  
  
  
  
  # 设置完毕后的.git中的config配置
  [core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = git@gitlab.com:yly_python/py_demo.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
	remote = origin
	merge = refs/heads/main
	vscode-merge-base = origin/main
[remote "gitcode"]  # 这就是咱们新加的远程推送
	url = git@gitcode.com:yaoliuyang/py_demo.git
	fetch = +refs/heads/*:refs/remotes/gitcode/*

```

**然后分别推送**

```shell
  git push origin main
  git push mirror main
```

