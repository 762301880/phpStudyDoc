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

##  前端访问后端代码显示 CORS 跨域配置

> nginx后端服务器nginx配置添加跨域设置

```nginx
add_header Access-Control-Allow-Origin *;
add_header Access-Control-Allow-Methods 'GET, POST, PUT, DELETE, OPTIONS';
add_header Access-Control-Allow-Headers 'DNT,X-Mx-ReqToken,Keep-Alive,User-Agent,X-Requested-With,If-Modified-Since,Cache-Control,Content-Type,Authorization,Signtoken,siteid,cityid,,site-id';

if ($request_method = 'OPTIONS') {
        return 204;
}
```

**解释**

1. **跨域基础设置**
   - `add_header Access-Control-Allow-Origin *;`
     允许任何域名的请求访问服务器资源（`*` 表示通配符，实际生产环境中建议指定具体域名以增强安全性）。
   - `add_header Access-Control-Allow-Methods 'GET, POST, OPTIONS';`
     允许的 HTTP 请求方法，包括 GET、POST 和 OPTIONS（预检请求）。
   - `add_header Access-Control-Allow-Headers ...;`
     允许请求中携带的自定义请求头，例如 `Authorization`、`Content-Type` 等，这里列出了前端可能会用到的头信息。

**注意**

```nginx
# 以下这俩不支持通配符*  需要 显式指定允许的请求头
add_header Access-Control-Allow-Methods
add_header Access-Control-Allow-Headers
```

