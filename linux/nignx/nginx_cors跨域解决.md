## 什么是 **CORS跨域**

CORS（Cross-Origin Resource Sharing，跨域资源共享）是一种浏览器安全机制，用来控制：

> 一个网站的前端页面，能不能访问另一个域名下的资源。

例如：

- 前端：`https://web.example.com`
- 后端 API：`https://api.example.com`

此时：

- 域名不同
- 浏览器会认为这是“跨域请求”

浏览器默认会拦截这种请求，除非后端明确允许。

### 一、什么叫“同源”

浏览器判断“同源”看三个东西：

| 项   | 示例          |
| ---- | ------------- |
| 协议 | http / https  |
| 域名 | a.com / b.com |
| 端口 | 80 / 8080     |

必须完全一致才叫同源。

例如

| 地址1              | 地址2                | 是否跨域 |
| ------------------ | -------------------- | -------- |
| `https://a.com`    | `https://a.com/api`  | 否       |
| `https://a.com`    | `http://a.com`       | 是       |
| `https://a.com`    | `https://b.com`      | 是       |
| `https://a.com:80` | `https://a.com:8080` | 是       |

### 二、为什么浏览器要限制跨域

这是浏览器的：

同源策略（Same-Origin Policy）

目的是防止：

- 恶意网站盗取用户信息
- 偷调用你的登录接口
- 窃取 Cookie
- CSRF 攻击

比如：

你登录了银行网站。

如果没有同源限制：

```
fetch('https://bank.com/user/money')
```

任何网站都能读取你的账户信息。

### 三、CORS 是怎么解决跨域的

核心思想：

浏览器先问后端：

> “这个域名能不能访问你？”

后端如果允许：

```
Access-Control-Allow-Origin: https://web.example.com
```

浏览器才放行。





## 我的报错情况说明

> 我本地vue项目请求个人云服务器接口报错 

### **报错信息**

```php
Access to XMLHttpRequest at 'http://47.107.33.56:1997/api/auth/login' from origin 'http://localhost:5173' has been blocked by CORS policy: Response to preflight request doesn't pass access control check: No 'Access-Control-Allow-Origin' header is present on the requested resource. Login.vue:45 POST http://47.107.33.56:1997/api/auth/login net::ERR_FAILED
```

## 原因排查

> 最终发现我虽然nginx配置里面设置了 跨域配置 但是请求发现还是跨域
>
> 从网上查询`Access-Control-Allow-Origin` 多了一个也会导致跨域问题
>
> Access-Control-Allow-Origin 只能设置在服务端 比如 nginx配置 或者后端代码 
>
> 后端代码全局搜索了一下 `Access-Control-Allow-Origin`  果然发现了一个以前装的插件**"fruitcake/laravel-cors": "^2.0"**
>
> 果断卸载

### 前端查看的**Response headers**

```php
HTTP/1.1 200 OK
Server: nginx/1.29.5
Content-Type: application/json
Transfer-Encoding: chunked
Connection: keep-alive
X-Powered-By: PHP/7.4.33
Cache-Control: no-cache, private
Date: Sun, 24 May 2026 06:13:44 GMT
Access-Control-Allow-Origin: * # 第一个
X-Frame-Options: SAMEORIGIN
X-XSS-Protection: 1; mode=block
X-Content-Type-Options: nosniff
Access-Control-Allow-Origin: *   # 多了一个
Access-Control-Allow-Methods: GET,POST,PUT,DELETE,OPTIONS
Access-Control-Allow-Headers: *   
```

##  nginx跨域配置

```nginx
    #servier块配置如下

    # ===================================
    # CORS
    # ===================================

    add_header Access-Control-Allow-Origin "*" always;
    add_header Access-Control-Allow-Methods "GET,POST,PUT,DELETE,OPTIONS" always;
    add_header Access-Control-Allow-Headers "*" always;

    # 预检请求
    if ($request_method = OPTIONS) {
        return 204;
    }
```

###  **注释版本**

```nginx
# ===================================
# CORS 跨域配置
# ===================================

# 允许所有域名跨域访问
# 生产环境建议改成具体域名，不要直接使用 *
# 例如：
# add_header Access-Control-Allow-Origin "https://admin.xxx.com" always;
add_header Access-Control-Allow-Origin "*" always;

# 允许的 HTTP 请求方法
add_header Access-Control-Allow-Methods "GET,POST,PUT,DELETE,OPTIONS" always;

# 允许客户端携带的请求头
# * 在部分浏览器兼容性一般，企业里通常明确写：
# Authorization,Content-Type,X-Requested-With
add_header Access-Control-Allow-Headers "*" always;

# ===================================
# OPTIONS 预检请求处理
# ===================================

# 浏览器在以下情况会先发送 OPTIONS 请求：
# - application/json
# - Authorization
# - PUT/DELETE/PATCH
# - 自定义 Header
#
# 如果不处理 OPTIONS：
# 浏览器会直接报 CORS 错误
if ($request_method = OPTIONS) {

    # 返回 204（No Content）
    # 表示预检通过，无需返回内容
    return 204;
}
```

## 天大的荒唐2(一定要注意)

###   本地节点代理翻墙导致跨域

> 明明已经解决了跨域请求又是 报错跨域,明明跨域配置已经配置过了
>
> 后面无意中把翻墙关了发现哎又可以请求通了 打开又是跨域 