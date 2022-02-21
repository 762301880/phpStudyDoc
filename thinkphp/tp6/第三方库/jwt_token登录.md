# 资料

| 名称            | 地址                                                         |
| --------------- | ------------------------------------------------------------ |
| 第三方博客参考  | [link](https://startmvc.com/article_9589.html)  [link](https://blog.csdn.net/linhuarui/article/details/113842697) |
| jwt-官网  jwt库 | [link](https://jwt.io/)   [link](https://jwt.io/libraries)   |

# 说明

> 关于jwt的介绍可以看[官网的文档](https://jwt.io/introduction),因为我以前已经写过一篇关于[jwt的文档](https://gitee.com/yaolliuyang/phpStudyDoc/blob/main/laravel/%E7%AC%AC%E4%B8%89%E6%96%B9%E5%BA%93/jwt-auth_laravel%E5%A4%9A%E8%A1%A8%E8%AE%A4%E8%AF%81.md)这里就不详细的介绍
>
> 标准声明字段：
>
> 接收该JWT的一方
>
> iss: jwt签发者
>
> sub: jwt所面向的用户
>
> aud: 接收jwt的一方
>
> exp: jwt的过期时间，过期时间必须要大于签发时间
>
> nbf: 定义在什么时间之前，某个时间点后才能访问
>
> iat: jwt的签发时间
>
> jti: jwt的唯一身份标识，主要用来作为一次性token。

# 缺点

> 由于jwt是由三部分组成的 头部 荷载 加密  一般我们会在**载荷**里面放入用户的信息，但是经过亲测 这里的数据只是简单的经过了
>
> base64加密所以只要拿到**载荷**部分信息使用**base64_decode()**函数是可以直接把关键信息解密出来的，所以强烈不推荐将**敏感**
>
> 信息放在**载荷**里面

# 代码使用示例

**创建对应路由**

```php
<?php

use think\facade\Route;

Route::group('api/v1', function () {
    Route::any('phpinfo', function () {
        # 访问  域名/auth/api/v1/phpinfo查看php信息
        return phpinfo();
    }); // 注册接口
    Route::post('register', 'Auth/register'); // 注册接口
    Route::post('login', 'Auth/login'); // 注册接口
});

# 半个月内免登陆，这里就要使用到了refreshToken了，jwt设计思想很到位：设置发给前端的token一个有效期，比如2个小时，2个小时候前端发来的token就会失效，这个时候我们根据发来的token判断下，如果这个token在2个小时外，并在刷新token的有效期内（比如半个月内），那么我们在给前端返回数据的时候返回一个新token，前端接到这个token存储起来，当再次请求的时候，发送新的token，如此周而复始，只要你在半个月内没有间断去进入系统，那么完全不需要去进行登录的操作。
```

# token无缝刷新

**参考**

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://blog.csdn.net/weixin_39581652/article/details/110801338)  [link](https://blog.csdn.net/pyycsd/article/details/102803133?spm=1001.2101.3001.6650.11&utm_medium=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-11.wap_blog_relevant_default&depth_1-utm_source=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-11.wap_blog_relevant_default)  [link](https://blog.csdn.net/weixin_44347271/article/details/121721121?spm=1001.2101.3001.6650.3&utm_medium=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.wap_blog_relevant_default&depth_1-utm_source=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.wap_blog_relevant_default) |
|                |                                                              |

