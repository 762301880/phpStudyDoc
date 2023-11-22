# 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| PicGo-文档 | [link](https://picgo.github.io/PicGo-Core-Doc/)  [link](https://picgo.github.io/PicGo-Doc/zh/guide/config.html#%E5%9F%BA%E6%9C%AC%E6%93%8D%E4%BD%9C%E9%A2%84%E8%A7%88) |



# Typora设置PicGo命令插件

> 打开导航栏的 **文件-偏好设置-图像**
>
> 记得使用时候必须**下载或更新**PicGo-Core(command line)

![image-20210429170919057](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210429170919057.png)

## [阿里云上传配置](https://picgo.github.io/PicGo-Core-Doc/zh/guide/config.html#picbed-aliyun)

**资料**

| 名称                          | 地址                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| 阿里云-oss官方文档 区域对照表 | [link](https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.11f97908uGYPQK#concept-zt4-cvy-5db) |

**说明**

> 打开配置文件设

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
    "customUrl": "https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com", # 自定义域名，注意要加 http://或者 https://  可以点击图片然后查询域名
    "options": "" # 针对图片的一些后缀处理参数 PicGo 2.2.0+ PicGo-Core 1.4.0+
             }
    },
    "picgoPlugins": {}
}

```



##  [SM.MS 上传配置](https://blog.csdn.net/netceor/article/details/119705826)

```php
{
  "picBed": {
    "current": "smms",
    "smms": {
      "token": ""   //你的smms_token
    }
  },
  "picgoPlugins": {}
}
```

