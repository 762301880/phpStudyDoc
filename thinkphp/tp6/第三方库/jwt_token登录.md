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

# refreshToken 建议有一个type去保存他的类型是refreshToken(然后再中间件中判断)以防万一有用户用这个token请求接口,只需要表明这个刷新token是这个用户的就可以了
```

# token无缝刷新

**参考**

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://blog.csdn.net/weixin_39581652/article/details/110801338)  [link](https://blog.csdn.net/pyycsd/article/details/102803133?spm=1001.2101.3001.6650.11&utm_medium=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-11.wap_blog_relevant_default&depth_1-utm_source=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-11.wap_blog_relevant_default)  [link](https://blog.csdn.net/weixin_44347271/article/details/121721121?spm=1001.2101.3001.6650.3&utm_medium=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.wap_blog_relevant_default&depth_1-utm_source=distribute.wap_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3.wap_blog_relevant_default)   [link](http://www.cxybb.com/article/desky/114640840) |
| jwt过期方案    | [link](https://cloud.tencent.com/developer/article/2001607)  |

> 看laravel那边的框架 旧token是存本地文件缓存叫做黑名单 刷新token的时候设置的旧黑名单



**很好的参考**

- **token过期的续期方案**
  - [**单token方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&chksm=fa496f8ecd3ee698f4954c00efb80fe955ec9198fff3ef4011e331aa37f55a6a17bc8c0335a8&scene=21&token=899450012&lang=zh_CN#wechat_redirect)
  - [**双token方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&chksm=fa496f8ecd3ee698f4954c00efb80fe955ec9198fff3ef4011e331aa37f55a6a17bc8c0335a8&scene=21&token=899450012&lang=zh_CN#wechat_redirect)
  - [**微信网页授权方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&chksm=fa496f8ecd3ee698f4954c00efb80fe955ec9198fff3ef4011e331aa37f55a6a17bc8c0335a8&scene=21&token=899450012&lang=zh_CN#wechat_redirect)

------

JWT token的 payload 部分是一个json串，是要传递数据的一组声明，这些声明被JWT标准称为claims。

JWT标准里面定义的标准claim包括：

- `iss(Issuser)`：JWT的签发主体；
- `sub(Subject)`：JWT的所有者；
- `aud(Audience)`：JWT的接收对象；
- `exp(Expiration time)`：JWT的过期时间；
- `nbf(Not Before)`：JWT的生效开始时间；
- `iat(Issued at)`：JWT的签发时间；
- `jti(JWT ID)`：是JWT的唯一标识。

除了以上标准声明以外，我们还可以自定义声明。以 com.auth0 为例，下面代码片段实现了生成一个带有过期时间的token.

```javascript
String token = JWT.create()
        .withIssuer(ISSUER)
        .withIssuedAt(new Date(currentTime))// 签发时间
        .withExpiresAt(new Date(currentTime + EXPIRES_IN * 1000 * 60))// 过期时间戳
        .withClaim("username", username)//自定义参数
        .sign(Algorithm.HMAC256(user.getPassword()));
```

复制

其中：

- `withIssuer() `设置签发主体；
- `withIssuedAt() `设置签发时间；
- `withExpiresAt() `设置过期时间戳，过期的时长为 EXPIRES_IN （单位秒）；
- `withClaim() `设置自定义参数。

JWT设置了过期时间以后，一定超过，那么接口就不能访问了，需要用户重新登录获取token。如果经常需要用户重新登录，显然这种体验不是太好，因此很多应用会采用token过期后自动续期的方案，只有特定条件下才会让用户重新登录。

## [**token过期的续期方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

解决token过期的续期问题可以有很多种不同的方案，这里举一些比较有代表性的例子。首先我们看一个单token方案，这个方案除了可以实现token续期以外，还可以实现某些条件下的强制重新登录。

### [**单token方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/e5e178a83c6156fd3215b77299dbdcae.png)

- 将 token 过期时间设置为15分钟；
- 前端发起请求，后端验证 token 是否过期；如果过期，前端发起刷新token请求，后端为前端返回一个新的token；
- 前端用新的token发起请求，请求成功；
- 如果要实现每隔72小时，必须重新登录，后端需要记录每次用户的登录时间；用户每次请求时，检查用户最后一次登录日期，如超过72小时，则拒绝刷新token的请求，请求失败，跳转到登录页面。

另外后端还可以记录刷新token的次数，比如最多刷新50次，如果达到50次，则不再允许刷新，需要用户重新授权。

上面介绍的单token方案原理比较简单。下面我们再看一个双token方案。

### [**双token方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

- 登录成功以后，后端返回 `access_token` 和 `refresh_token`，客户端缓存此两种token;
- 使用 `access_token` 请求接口资源，成功则调用成功；如果token超时，客户端携带 `refresh_token` 调用token刷新接口获取新的 `access_token`;
- 后端接受刷新token的请求后，检查 `refresh_token` 是否过期。如果过期，拒绝刷新，客户端收到该状态后，跳转到登录页；如果未过期，生成新的 `access_token` 返回给客户端。
- 客户端携带新的 `access_token` 重新调用上面的资源接口。
- 客户端退出登录或修改密码后，注销旧的token，使 `access_token` 和 `refresh_token` 失效，同时清空客户端的 `access_token` 和 `refresh_toke`。

微信网页授权是通过OAuth2.0机制实现的，也使用了双token方案。

![img](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/152a9f9d28a4f3721729894e6e378ecb.png)

### [**微信网页授权方案**](https://mp.weixin.qq.com/s?__biz=MzUzMTA2NTU2Ng==&mid=2247487551&idx=1&sn=18f64ba49f3f0f9d8be9d1fdef8857d9&scene=21#wechat_redirect)

- 用户在第三方应用的网页上完成微信授权以后，第三方应用可以获得 code（授权码）。code的超时时间为10分钟，一个code只能成功换取一次access_token即失效。
- 第三方应用通过code获取网页授权凭证access_token和刷新凭证 refresh_token。
- access_token是调用授权关系接口的调用凭证，由于access_token有效期（2个小时）较短，当access_token超时后，可以使用refresh_token进行刷新。
- refresh_token拥有较长的有效期（30天），当refresh_token失效的后，需要用户重新授权。

后端实现token过期还可以利用Redis来存储token，设置redis的键值对的过期时间。如果发现redis中不存在token的记录，说明token已经过期了。