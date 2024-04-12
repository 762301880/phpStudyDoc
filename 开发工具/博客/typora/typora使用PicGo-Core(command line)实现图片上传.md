# 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| PicGo-文档 | [link](https://picgo.github.io/PicGo-Core-Doc/)  [link](https://picgo.github.io/PicGo-Doc/zh/guide/config.html#%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E9%A2%84%E8%A7%88) |



# Typora设置PicGo命令插件

> 打开导航栏的 **文件-偏好设置-图像**
>
> 记得使用时候必须**下载或更新**PicGo-Core(command line)

![image-20210429170919057](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210429170919057.png)

## [阿里云上传配置](https://picgo.github.io/PicGo-Core-Doc/zh/guide/config.html#picbed-aliyun)

**资料**

| 名称                          | 地址                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| 阿里云-oss官方文档 区域对照表 | [link](https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.11f97908uGYPQK#concept-zt4-cvy-5db) |

**说明**

> 打开配置文件设
>
> **配置文件地址  C:\Users\yaoliuyang\.picgo\config.json**

```shell
{
    "picBed": {
    "uploader": "aliyun",
    "aliyun": {
    "accessKeyId": "******************",#您的阿里云accessKeyId
    "accessKeySecret": "****************",#您的阿里云accessKeySecret
    "bucket": "yaoliuyang-blog-images", # 存储空间名-Bucket
    "area": "oss-cn-beijing", # 存储区域代号-不知到的可以对照上图资料对照表去查
    "path": "blogImages/", # 自定义存储路径-定义需要存储图片的文件
    "customUrl": "https://gitee.com/yaolliuyang/blogImages/raw/master", # 自定义域名，注意要加 http://或者 https://  可以点击图片然后查询域名
    "options": "" # 针对图片的一些后缀处理参数 PicGo 2.2.0+ PicGo-Core 1.4.0+
             }
    },
    "picgoPlugins": {}
}

```



##  [SM.MS 上传配置-不推荐](https://blog.csdn.net/netceor/article/details/119705826)

> 不推荐原因：访问过于慢且有容量限制 并且容易被封

```php
# https://picgo.github.io/PicGo-Core-Doc/zh/guide/config.html#%E8%87%AA%E5%8A%A8%E7%94%9F%E6%88%90

{
  "picBed": {
    "uploader": "smms", // 代表当前的默认上传图床为 SM.MS,
    "smms": {
      "token": "" // 从 https://sm.ms/home/apitoken 获取的 token
    }
  },
  "picgoPlugins": {} // 为插件预留
}
```

## [gitee图床](https://picgo.github.io/PicGo-Doc/zh/guide/config.html#github%E5%9B%BE%E5%BA%8A)(推荐)

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/m0_37952030/article/details/109138431)  [link](https://www.cnblogs.com/iangel/p/15131181.html) |

### 安装picgo插件

**安装picgo**

> ## 安装 picgo (core)
>
> 1. 前提是安装好了 nodejs，命令行能运行 npm
>    具体说明可以参考：[此处廖雪峰博客](https://www.liaoxuefeng.com/wiki/1022910821149312/1023025597810528)
> 2. 在 cmd 窗口运行 

```shell
npm install picgo -g
```

**安装 picgo 插件**

> 1. 用于使用gitee作为图床 和 上传图片时能在文件前缀加上时间戳

```shell
picgo install gitee-uploader super-prefix
```



### 准备步骤

**新建仓库**

![image-20240412135256137](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412135256137.png)

**设置私人令牌-token**

![image-20240412135438152](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412135438152.png)

![image-20240412135452541](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412135452541.png)

### 对应picgo-core代码配置

![image-20240412135529640](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412135529640.png)

```shell
{
  "repo": "", // 仓库名，格式是username/reponame
  "token": "", // github token
  "path": "", // 自定义存储路径，比如img/
  "customUrl": "", // 自定义域名，注意要加http://或者https://
  "branch": "" // 分支名，默认是main
}


# 个人配置

{
  "picBed": {
    "uploader": "gitee",
    "gitee": {
      "repo": "yaolliuyang/blogImages",
      "token": "****************",
      "path": "blogImages/",
      "customUrl": "",
      "branch": "master"
    }
  },
  "picgoPlugins": {
    "picgo-plugin-gitee-uploader": true,
    "picgo-plugin-super-prefix": true
  },
  "picgo-plugin-gitee-uploader": {
    "lastSync": "2024-04-12 01:46:57"
  }
}
```

# 图片迁移

## 阿里云oss图片迁移gitee仓库

> 可以一键替换前缀

![image-20240412151212574](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412151212574.png)