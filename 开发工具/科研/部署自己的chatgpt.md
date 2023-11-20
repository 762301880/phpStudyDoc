#  首先你要有自己的外网服务器



## [Yidadaa/ChatGPT-Next-Web](https://github.com/Yidadaa/ChatGPT-Next-Web) 部署

**docker**部署

```shell
docker pull yidadaa/chatgpt-next-web

docker run -d -p 3000:3000 \
   -e OPENAI_API_KEY=sk-xxxx \
   -e CODE=your-password \
   yidadaa/chatgpt-next-web
```

# 补充

## 免费的chatgpt-api 申请平台

| name     | url                                                  |
| -------- | ---------------------------------------------------- |
| github   | [link](https://github.com/chatanywhere/GPT_API_free) |
| 网络博客 | [link](https://zhuanlan.zhihu.com/p/648613772)       |

## https://github.com/chatanywhere/GPT_API_free 

用法

点击**[申请领取内测免费API Key](https://api.chatanywhere.org/v1/oauth/free/github/render)**

**配置**

> - 转发Host1: `https://api.chatanywhere.com.cn` (国内中转，延时更低，推荐)
> - 转发Host2: `https://api.chatanywhere.cn` (国外使用,国内需要全局代理)

> 把对应的host地址填写到接口地址上面
>
> 把对应的申请的api key填写到申请的api key 上面

![image-20231120163328201](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20231120163328201.png)