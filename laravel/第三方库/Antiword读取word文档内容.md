#  说明

> 听说下个月有一个读取word文档简历内容然后自动保存简历到后台的功能。百度了一番发现有一个包
>
> 很不错，但是美中不足无法支持 excel，docx，txt,pdf 这几种数据格式。只持支doc格式内容
>
> 不管了玩玩看再说

# Antiword软件[安装](http://www.winfield.demon.nl/)

## windows安装

[点击下载软件](http://www.winfield.demon.nl/dos/antiword.zip)

**设置环境变量**

```shell
鼠标右键-此电脑-属性-高级系统设置-环境变量-系统变量-将下载解压的Antiword目录写在Path里
```

![1640311387(1).jpg](https://s2.loli.net/2021/12/24/Yzl6NHe7udwmT5M.png)

[**简单测试**](https://blog.csdn.net/qq_29099209/article/details/79904417)

```shell
antiword -h
# 测试输出文件内容
antiword –t 文件名.doc
```

