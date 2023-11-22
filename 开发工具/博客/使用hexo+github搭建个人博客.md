#  一:介绍

## 1.[hexo](https://hexo.io/zh-cn/)介绍

> Hexo 是一个快速、简洁且高效的博客框架。Hexo 使用 [Markdown](http://daringfireball.net/projects/markdown/)（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页
>
> 今天我们就基于hexo+github pages服务搭建一个个人博客

#  二:需要的工具安装

## 2.1 Git

   ### [git安装](https://www.cnblogs.com/yaoliuyang/p/13359382.html)

### [git使用](https://www.cnblogs.com/yaoliuyang/p/13052292.html) 

## 2.2 Node

### [node.js安装](https://www.cnblogs.com/yaoliuyang/p/12652005.html)

### [node.js修改源](https://www.cnblogs.com/yaoliuyang/p/12689814.html)



# 三:安装与使用

##  3.1 参考hexo的[官网文档](https://hexo.io/zh-cn/docs/)安装

> 使用npm命令安装hexo

```shell
npm install -g hexo-cli 
```

> 将 Hexo 所在的目录下的 `node_modules` 添加到环境变量之中即可直接使用 `hexo <command>`

```shell
echo 'PATH="$PATH:./node_modules/.bin"' >> ~/.profile
```

> 安装完成之后检查是否安装成功

```shell
hexo -v
```

> 出现以下命令则安装成功

```shell
hexo-cli: 4.2.0
os: Windows_NT 10.0.18363 win32 x64
node: 14.15.1
v8: 8.4.371.19-node.17
uv: 1.40.0
zlib: 1.2.11
brotli: 1.0.9
ares: 1.16.1
modules: 83
nghttp2: 1.41.0
napi: 7
llhttp: 2.1.3
openssl: 1.1.1g
cldr: 37.0
icu: 67.1
tz: 2020a
unicode: 13.0

```

## 3.2 使用hexo命令建站

安装 Hexo 完成后，请执行下列命令，Hexo 将会在指定文件夹中新建所需要的文件

> 初始化一个项目

```shell
hexo init <folder> #<folder>等于你的文件夹名称
```

下载完成之后的文件

![image-20210428154950482](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428154950482.png)

在本地查看hexo博客

```shell
hexo s
# 显示结果
INFO  Validating config
INFO  Start processing
INFO  Hexo is running at http://localhost:4000 . Press Ctrl+C to stop.
```

然后你就可以在本地使用域名 localhost:4000查看本地博客了

![image-20210428163203529](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428163203529.png)

## 3.3 修改hexo的[主题](https://hexo.io/themes/)

> 以上操作完成的时候你会发现怎么界面这么丑，还好hexo为我们贴心的准备了很多扩展包主题

打开hexo[主题网站](https://hexo.io/themes/)下载我们需要的主题

> 我们可以选择一个自己喜欢的主题:使用gie拉去代码
>
> 在项目的根目录中执行命令=>此命令会在thems目录中将拉取到的代码命名为stun

```shell
git clone git@github.com:liuyib/hexo-theme-stun.git themes/stun
```

这里注意有的主题包需要安装依赖，(这个在对应的主题包下面有讲解)

![image-20210428170533008](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428170533008.png)

> 比如我这个主题包安装的依赖
>
> 在项目中执行命令

```shell
npm install --save hexo-renderer-pug
```



## 3.4 编辑配置_config.yml

> 以上只是拉取皮肤文件，想要主题显示我们还需要配置一下
>
> 打开_config.yml文件

```shell
language: zh-CN         # 将语言修改为中文 此文件可以在下载的主题包中\themes\stun\languages中有对应的文件
theme: stun             # stun 等于你下载的主题包
hexo clean && hexo s    # 启动hexo 启动之后再次打开域名 localhost:4000您就可以看见新的主题了
```

# 四: github & pages配置

## 4.1 新建仓库

> 1.仓库名称必须对应  `您的名称+.github.io`
>
> 2.必须必须是public

![image-20210428170830749](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428170830749.png)

在项目中使用git命令将项目同步到github

```shell
git init
git add .
git commit -m "init"
git remote add origin https://github.com/762301880/762301880.github.io.git
git push -u origin master
```

## 4.2 将项目上传git

### 4.2.1  配置__config.yml

```shell
deploy:
  type: git
  repo: https://github.com/CrimsonRomance/CrimsonRomance.github.io   #等于你上传的github仓库的地址
  branch: master
```



> 回到命令行窗口，输入**npm install hexo-deployer-git --save,**
>
> 再输入**hexo g，**然后再**hexo d**，就可以将我们public里面的代码上传咯，在GitHub上可以看到我们上传的代码。这样别人也可以通过域名访问我们博客了。在地址栏输入http://域名就可以访问。

```shell
npm install hexo-deployer-git --save
hexo g   #  生成
hexo d   #  部署  只有你上一步配置了才能成功的上传github
```





## 4.3  点击settings

![image-20210428171402598](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428171402598.png)

![image-20210428173555973](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428173555973.png)



然后打开上面的域名即可访问你上传成功的博客

![image-20210428173706898](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210428173706898.png)





# 五：如果换了电脑如何上传hexo（此方案已废弃）

首先你需要把你本地的hexo上传到远程github分值 

```shell
git branch hexo # 创建分支
git checkout hexo # 切换分支
git add . # 提交全部
git commit -m '提交分值hexo' 
git push origin hexo #提交到远程分值hexo
```

# 参考资料

| 第三方皮肤网站                                          | 第三方博客                                                   |
| ------------------------------------------------------- | ------------------------------------------------------------ |
| [Volantis](https://volantis.js.org/v5/getting-started/) | [Hexo + GitHub (Coding) Pages 搭建博客](https://github.com/HarleyWang93/blog/issues/1) |

