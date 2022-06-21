# 资料

| 说明                                 | 地址                                                         |
| ------------------------------------ | ------------------------------------------------------------ |
| 更多配置请查阅玩转 PhpStorm 系列教程 | [link](https://laravelacademy.org/books/phpstorm-tutorial)   |
| 第三方博客                           | [php storm手册](https://www.kancloud.cn/ervinhua/phpstorm/441649) |



# 配置deployment(代码上传服务器)

## 说明

> 我们本地开发的时候有时候想快点在测试服测试一下数据，但是又不想提交到测试服或则jenkins同步的比较慢
>
> 这个时候就可以使用phpstorm自带的deployment配置SFTP上传代码

## 步骤

1. 点击导航栏的**Tools**-**Deployment**-**Configuration...**

![1634177504(1).png](https://i.loli.net/2021/10/14/ZcxPleKGzOa7LR9.png)

![1634178544(1).jpg](https://i.loli.net/2021/10/14/Oi5xzv48kAoyuN6.png)

![1634178580(1).png](https://i.loli.net/2021/10/14/RA9jMriHkuqKptn.png)

2.  配置连接

> 点击右上角的+选择SFTP 编辑连接

![1634177862(1).png](https://i.loli.net/2021/10/14/2flsMqT9YBwKGmV.png)





- 设置自动上传

> 点击导航栏的**Tools**-**Deployment**-**Options...**-

![1634178390(1).jpg](https://i.loli.net/2021/10/14/W91Mf85ajHBgEux.png)

> 自此意见全部配置完成 修改的时候直接点击文件右键点击***Deployment***- **Uploadto*你的项目名称**即可

# [配置terminal**终端** 为git终端](https://learnku.com/articles/32981)



> 我们默认使用phpstorm自带的终端的时候默认配置的是windows自带的**cmd** 终端，如果我们在使用 ls rm -rf 等常用命令的时候
>
> 就会很难受又不想再下载一个第三方终端软件的情况下，可以将phpstorm自带的终端修改为git 终端我们都知道git终端自带支持了
>
> 很多linux命令

**修改phpstorm 命令终端为git 终端**

> 点击导航栏的**file->settings->Tools->Terminal** 将 一下标记的地方修改为你git安装目录 **bin**目录下面的**bash.exe**然后重启phpstorm即可

![1645685051(1).jpg](https://s2.loli.net/2022/02/24/qxPtJphZOEvIViw.png)

#  快捷键

```php
# 折叠&取消折叠方法  https://www.cnblogs.com/kevin-yang123/p/10138633.html
`Ctrl`+`Shift`+`-`    //折叠方法
`Ctrl`+`Shift`+`+`    //取消折叠方法    
```

