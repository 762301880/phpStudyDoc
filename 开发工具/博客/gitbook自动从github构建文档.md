## 这个部署比较无脑 就是自动同步有点问题

我新弄了一个go文档死活就是没法同步  最终解决了 

点击github头像 **settings**  找到**Applications**  

找到 GitBook.com 程序点击后面的**Configure** 设置**Repository access** 为**All repositories**

> 我是怎么发现这里的





![image-20260501210418879](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20260501210418879.png)

![image-20260501210424140](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20260501210424140.png)



## 注意事项

### 休息日的时候 gitbook官方因该会停止服务器同步工作,所以工作日才会正常同步

###  禁止gitbook自动推送垃圾文件到gitbook

> 事情原因 不知道什么bug  gitbook 反向推送了很多垃圾代码到gitbook只好禁止垃圾代码的推送

#### 方案一(设置仓库禁止gitbook-bot反向推送)

1. 点击仓库的**Settings**   (注意是单个仓库的设置)
2. 找到**Branches**   给 `main`（或默认分支）创建保护规则  **Add branch ruleset** 
3. **Ruleset Name*** 名称随便取
4. **Enforcement status** 设置为**Active**
5. **Bypass list** 添加头两个
6. **Target branches** 分支设置为你需要保护的分支