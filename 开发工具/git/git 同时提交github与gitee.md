# 资料

| 名称           | 地址                                                |
| -------------- | --------------------------------------------------- |
| 第三方博客参考 | [link](https://segmentfault.com/a/1190000019822601) |
|                |                                                     |



# 说明

> 个人比较喜欢在**github**上面存放一些东西,但是有时候又怕**github**突然有一天崩了,于是想着同步一份到**gitee**,双份保险
>
> 但是问题又来了,每次我提交到**github**的代码还需要手动去**gitee**点一下同步很是麻烦

## 设置步骤

**开放隐藏的项目 .git**

> 进入项目目录点击导航栏的查看勾选隐藏的项目，linux用户请 **ls -a**可以显示隐藏的项目

![image-20220126114827546](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220126114827546.png)

**选择配置文件**

> 进入**.git** 选择**config**使用编辑器打开

![image-20220126115024702](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220126115024702.png)

## 设置**config**的两种方式

**第一种方式**

```shell
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = git@github.com:762301880/项目名称.git
	url = git@gitee.com:yaolliuyang/项目名称.git # 赋值一份gitee的ssh git地址即可一般邮箱用的都是同一个所以不用配置
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
	remote = origin
	merge = refs/heads/main
```

**第二种方式**

```shell
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = git@github.com:762301880/项目名称.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[remote "origin"] 
	url = git@gitee.com:yaolliuyang/项目名称.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
	remote = origin
	merge = refs/heads/main
	
# 补充开始
[core]
	repositoryformatversion = 0
	filemode = false
	bare = false
	logallrefupdates = true
	symlinks = false
	ignorecase = true
[remote "origin"]
	url = git@github.com:762301880/项目名称.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[remote "mirror"] 
	url = git@gitee.com:yaolliuyang/项目名称.git
	fetch = +refs/heads/*:refs/remotes/origin/*
[branch "main"]
	remote = origin
	remote = mirror
	merge = refs/heads/main	
# 用这种方法需要推送2次
git push origin
git push mirror	
# 补充j
```

