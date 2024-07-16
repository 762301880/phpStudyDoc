# 解决谷歌浏览器无法右键翻译

## 事件原因

> 简单来说就是谷歌厂家认为翻译功能国内的人用的人太少了于是把国内服务停用了  就是停用了
>
> 国内的**translate.google.cn**域名

## 解决参考

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://github.com/hcfyapp/crx-selection-translate/discussions/1526#discussioncomment-3956157)  [link](https://www.bootwiki.com/note/21449) |



**修改hosts文件**

> **windows:hosts地址**   `C:\Windows\System32\drivers\etc\hosts`
>
> dns 查询  https://tool.chinaz.com/dns/translate.google.cn

##  [github 扫描端口插件](https://github.com/Ponderfly/GoogleTranslateIpCheck?tab=readme-ov-file)

> [博客](https://blog.csdn.net/totramp/article/details/132915125?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-3-132915125-blog-138306835.235%5Ev43%5Epc_blog_bottom_relevance_base7&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ECtr-3-132915125-blog-138306835.235%5Ev43%5Epc_blog_bottom_relevance_base7&utm_relevant_index=4)

# 翻译插件安装

## [沉浸式翻译](https://immersivetranslate.com/)

#  [在使用谷歌浏览器的时候出现不是私密连接的提示。](https://www.jb51.net/softjc/923215.html)

![img](https://img.jbzj.com/file_images/article/202401/20240119163949136.jpg)

#  谷歌浏览器无法访问原因

> [参考博客](https://blog.csdn.net/wxh0000mm/article/details/127204700#:~:text=%E8%80%8C%E4%B9%8B%E6%89%80%E4%BB%A5%E8%BF%91%E6%9C%9F%E7%AA%81%E7%84%B6%E6%97%A0%E6%B3%95%E4%BD%BF%E7%94%A8%E4%BA%86%EF%BC%8C%E6%98%AF%E5%9B%A0%E4%B8%BA%20%E8%B0%B7%E6%AD%8C%E5%85%B3%E9%97%AD%E4%BA%86%E5%9B%BD%E5%86%85%E7%9A%84%E8%B0%B7%E6%AD%8C%E7%BF%BB%E8%AF%91%E7%BD%91%E9%A1%B5%E7%89%88%20translate.google.cn%20%EF%BC%8C%E5%9B%A0%E6%AD%A4%E8%BF%9E%E5%B8%A6%E7%9D%80%E5%AF%BC%E8%87%B4%E8%B0%B7%E6%AD%8C%E7%BF%BB%E8%AF%91%20API%20%E6%8E%A5%E5%8F%A3%E5%9F%9F%E5%90%8D%E7%9A%84%E8%A7%A3%E6%9E%90%E4%B9%9F%E4%BB%8E%E5%9B%BD%E5%86%85,IP%20%E6%94%B9%E5%88%B0%E4%BA%86%E4%B8%8D%E5%8F%AF%E7%94%A8%E7%9A%84%E8%B0%B7%E6%AD%8C%E5%9B%BD%E5%A4%96%20IP%EF%BC%8C%E8%80%8C%E8%B0%B7%E6%AD%8C%E5%9B%BD%E5%86%85%E6%9C%8D%E5%8A%A1%E5%99%A8%E4%BE%9D%E7%84%B6%E5%8F%AF%E7%94%A8%EF%BC%8C%E6%89%80%E4%BB%A5%E5%8F%AA%E9%9C%80%E6%89%8B%E5%8A%A8%E5%9C%A8%20Hosts%20%E6%96%87%E4%BB%B6%E4%B8%AD%E5%B0%86%E5%9F%9F%E5%90%8D%E6%8C%87%E5%90%91%E8%B0%B7%E6%AD%8C%E5%9B%BD%E5%86%85%E6%9C%8D%E5%8A%A1%E5%99%A8%20IP%20%E5%8D%B3%E5%8F%AF%E3%80%82)

> ### 为啥翻译用不了？
>
> Chrome 浏览器自带的谷歌翻译，调用的 API 接口域名为：`translate.googleapis.com`   `translate-pa.googleapis.com`

![image-20240716093158782](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240716093158782.png)

![image-20240716093218626](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240716093218626.png)

**我们用站长工具查询出中国的dns写入即可**

![image-20240716093427039](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240716093427039.png)