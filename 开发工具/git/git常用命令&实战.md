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