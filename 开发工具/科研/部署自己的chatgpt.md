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

