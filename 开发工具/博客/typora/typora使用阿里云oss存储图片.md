# 说明

> 本来本人倾向于使用第三方的博客园进行编写博客，奈何最近博客园有点坑爹
>
> 不仅发布博客需要审核，而且还时不时的升级，一升级我们就没法编写博客
>
> 无奈只好转移到本地编写，然后直接提交git,无奈又发现提交到git无法显示图片
>
> 网上给的方案大多是使用picGo提交到github或者gitee但是这两个仓库的存储只有
>
> 一个G所以考虑放在阿里云oss中好在PicGo也提交了这个方案

# 使用

##  参考资料

| 第三方博客                                                |      |
| --------------------------------------------------------- | ---- |
| [链接](https://www.cnblogs.com/xuexianqi/p/13490854.html) |      |





## PicGo安装与配置

github下载[点我跳转](https://github.com/Molunerfinn/PicGo/releases) 或者直接下载[点我下载](https://github.com/Molunerfinn/PicGo/releases/download/v2.3.0-beta.6/PicGo-Setup-2.3.0-beta.6.exe)

`下载安装完成之后我们打开PicGo=>图床设置=>阿里云oSS`

> 在里面填写对应的信息

![image-20210426155750258](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426155750258.png)

`KeyId &&KeySecret` 对应

![image-20210426153948471](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426153948471.png)

`设定存储空间名`对应

![image-20210426154114077](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426154114077.png)

`确认存储区域`对应

![image-20210426153255428](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426153255428.png)

`指定存储路径`对应

![image-20210426154537023](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426154537023.png)

`上述完成之后可以使用picGo测试以下是否可以上传`

![image-20210426155034764](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426155034764.png)

##  阿里云oss设置

> 这里不详细解说，不懂的可以自行百度

`新建bucket`

![image-20210426154855422](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426154855422.png)



## Typora设置

在typora中点击文件=>偏好设置=>图像

![image-20210426155447146](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210426155447146.png)

#  使用

> 在编写markdown文档的时候直接邮件点击上传图片即可