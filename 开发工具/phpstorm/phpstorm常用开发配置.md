



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

![1634177504(1).png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/ZcxPleKGzOa7LR9.png)

![1634178544(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/Oi5xzv48kAoyuN6.png)

![1634178580(1).png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/RA9jMriHkuqKptn.png)

2.  配置连接

> 点击右上角的+选择SFTP 编辑连接

![1634177862(1).png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/2flsMqT9YBwKGmV.png)





- 设置自动上传

> 点击导航栏的**Tools**-**Deployment**-**Options...**-

![1634178390(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/W91Mf85ajHBgEux.png)

> 自此意见全部配置完成 修改的时候直接点击文件右键点击***Deployment***- **Uploadto*你的项目名称**即可

# [配置terminal**终端** 为git终端](https://learnku.com/articles/32981)



> 我们默认使用phpstorm自带的终端的时候默认配置的是windows自带的**cmd** 终端，如果我们在使用 ls rm -rf 等常用命令的时候
>
> 就会很难受又不想再下载一个第三方终端软件的情况下，可以将phpstorm自带的终端修改为git 终端我们都知道git终端自带支持了
>
> 很多linux命令

**修改phpstorm 命令终端为git 终端**

> 点击导航栏的**file->settings->Tools->Terminal** 将 一下标记的地方修改为你git安装目录 **bin**目录下面的**bash.exe**然后重启phpstorm即可

![1645685051(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/qxPtJphZOEvIViw.png)

#  快捷键

```php
# 折叠&取消折叠方法  https://www.cnblogs.com/kevin-yang123/p/10138633.html
`Ctrl`+`Shift`+`-`    //折叠方法
`Ctrl`+`Shift`+`+`    //取消折叠方法    
```

# 显示类分割线

![image-20240326110608598](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240326110608598.png)



![image-20240326110630959](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240326110630959.png)



![image-20240326110642383](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240326110642383.png)

# 搞笑bug

## **ctrl+shift+f**全文搜索小弹窗消失的搞笑

> 今天突然发现全文搜索小弹窗没了这很不方便利于修改代码，于是咨询了同事结果人家跟我说往上托就行了，真尴尬啊

![image-20221022162921948](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20221022162921948.png)

**想要的效果**

![image-20221022162837947](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20221022162837947.png)

# phpstorm优化内存设置

**资料**

| name       | url                                                 |
| ---------- | --------------------------------------------------- |
| 第三方博客 | [link](https://segmentfault.com/a/1190000013914840) |

> PHPStorm依赖java运行环境，说白了也就是java虚拟机，找到`help > Edit Custom VM Options`，然后在这个文件里可以根据需要增加或减少PHPstorm使用的内存



**对应设置**

```shell
# 原文件设置备份
-Xms128m
-Xmx2042m
-XX:ReservedCodeCacheSize=512m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:CICompilerCount=2
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-ea
-Dsun.io.useCanonCaches=false
-Djdk.http.auth.tunneling.disabledSchemes=""
-Djdk.attach.allowAttachSelf=true
-Djdk.module.illegalAccess.silent=true
-Dkotlinx.coroutines.debug=off

# 修改后的设置

-Xms128m
-Xmx1024m
-XX:ReservedCodeCacheSize=512m
-XX:+UseConcMarkSweepGC
-XX:SoftRefLRUPolicyMSPerMB=50
-XX:CICompilerCount=2
-XX:+HeapDumpOnOutOfMemoryError
-XX:-OmitStackTraceInFastThrow
-ea
-Dsun.io.useCanonCaches=false
-Djdk.http.auth.tunneling.disabledSchemes=""
-Djdk.attach.allowAttachSelf=true
-Djdk.module.illegalAccess.silent=true
-Dkotlinx.coroutines.debug=off
```

