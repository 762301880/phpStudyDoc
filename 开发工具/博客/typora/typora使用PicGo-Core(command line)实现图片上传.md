# 资料

| 名称       | 地址                                            |
| ---------- | ----------------------------------------------- |
| PicGo-文档 | [link](https://picgo.github.io/PicGo-Core-Doc/) |



# Typora设置PicGo命令插件

> 打开导航栏的 **文件-偏好设置-图像**
>
> 记得使用时候必须**下载或更新**PicGo-Core(command line)

![image-20210429170919057](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210429170919057.png)

## [阿里云上传配置](https://picgo.github.io/PicGo-Core-Doc/zh/guide/config.html#picbed-aliyun)

> 打开配置文件设置

```shell
{
    "picBed": {
    "uploader": "aliyun",
    "aliyun": {
    "accessKeyId": "******************",#您的阿里云accessKeyId
    "accessKeySecret": "****************",#您的阿里云accessKeySecret
    "bucket": "yaoliuyang-blog-images", # 存储空间名
    "area": "oss-cn-beijing", # 存储区域代号
    "path": "blogImages/", # 自定义存储路径
    "customUrl": "https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com", # 自定义域名，注意要加 http://或者 https://
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

