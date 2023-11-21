# nginx 解决 405 not allowed错误

**参考资料**

| 名称       | 地址                                           |
| ---------- | ---------------------------------------------- |
| 第三方博客 | [link](https://www.jianshu.com/p/9ede80f2b876) |

**解决方案**

```shell
    # 添加配置 405报错
    error_page  405 =200 @405;
     location @405 {
      proxy_method GET;
      proxy_pass http://localhost:8090;
    }
```

