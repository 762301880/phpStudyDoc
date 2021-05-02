# 设置

* 打开导航栏的 `文件-偏好设置-图像`
* `记得使用时候必须下载或更新PicGo-Core(command line)`

![image-20210429170919057](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210429170919057.png)

* 打开配置文件设置

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

