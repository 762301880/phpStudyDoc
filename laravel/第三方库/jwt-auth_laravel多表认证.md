# 说明&资料

## 说明

> [此段说明截取第三方博客](https://laravelacademy.org/post/9794)

## 传统比较

> session
>
> 最早期开发我们都会使用**session**存放在服务器端通过session访问用户数据(缺点集群无法共享)
>
> token
>
> Token+Redis  第一次登录之后生成token返回给前端并保存到session中(key为token value为用户id)然后以后前端就可以
>
> 携带token查询用户id 再DB查询数据库返回用户的详细信息,依赖于Redis真实token存放value值（缺点 每次都需要根据token查询真实内容
>
> 对服务器端的压力非常大）

### 什么是jwt

> **jwt**单词代表的意思就是**JSON Web Token**。
>
> 我们在做api请求的时候，通常要使用token，来验证是否这个请求能不能访问。
>
> 一旦用户登录，后续每个请求都将包含JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的JWT的一个特性，因为它的开销很小，并且可以轻松地跨域使用。

### 为什么使用jwt

> Session是在服务器端的，而JWT是在客户端的。
>
> Session方式存储用户信息的最大问题在于要占用大量服务器内存，增加服务器的开销，而JWT方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。

**优点&缺点**

> 优点
>
> token不会存储于服务端，不会占用服务端资源
>
> 缺点
>
> 每次请求都要解密token

### 详细解释

| 名称              | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| JWT.IO            | [link](https://jwt.io/)                                      |
| 网络上好的jwt博客 | [link](https://learnku.com/articles/12616/about-jwt-you-should-know) |

![1639309721(1).jpg](https://s2.loli.net/2021/12/12/OtSFRlnJpeTcD9w.png)

> 可以看出加密返回的***jswebtoken***分为三部分返回 使用***.***分割,右边是对应的解密信息
>
> 第一部分：为HEADER部分**header**的主要作用就是对数据进行描述,这里表示数据加密的
>
> 时候使用的是什么算法 alg(algorithm:算法)
>
> 第二部分：PAYLOAD(其实就是body部分)   这里是我们最核心的数据部分
>
> 第三部分：验证部分用的签名方式, (base64编码加上 header+一个点号，再使用base64加密有效数据(payload),
>
> 最后再传递一个256位的secret)





# tymon/jwt-auth

| name                       | url                                                          |
| -------------------------- | ------------------------------------------------------------ |
| 第三方博客                 | [link](https://learnku.com/articles/30342)   [link ](http://www.45fan.com/article.php?aid=19121863245239341202671983) [link](https://learnku.com/articles/7264/using-jwt-auth-to-implement-api-user-authentication-and-painless-refresh-access-token) |
| jwt-auth Wiki文档          | [link](https://jwt-auth.readthedocs.io/en/develop/laravel-installation/) |
| packagist项目地址          | [link](https://packagist.org/packages/tymon/jwt-auth)        |
| jwt超详细解释              | [link](https://learnku.com/articles/17883)                   |
| laravel学院                | [link](https://laravelacademy.org/post/9178)                 |
| laravel-wikis-jwt认证详解  | [link](https://learnku.com/laravel/wikis/25704)              |
| laravel学院扩展包-登陆认证 | [link](https://laravelacademy.org/post/3640)                 |

## [使用实例](https://learnku.com/articles/10885/full-use-of-jwt)  

[参考二](https://cloud.tencent.com/developer/article/1816522?from=15425)  [参考三](https://learnku.com/articles/10885/full-use-of-jwt)

## 安装

- 使用composer安装

```php
composer require tymon/jwt-auth
```

## 配置

- 添加服务提供者

>1. 添加服务提供者（Laravel 5.4 或以下）
>
>2. 将服务提供者添加到配置文件中的`providers`数组中`config/app.php`，如下所示：

```php
# 在项目目录\config\app.php中的providers中添加
'providers' => [
    ...
    Tymon\JWTAuth\Providers\LaravelServiceProvider::class,
]
'aliases' => [
	'JWTAuth'=> Tymon\JWTAuth\Facades\JWTAuth::class,
    'JWTFactory'=> Tymon\JWTAuth\Facades\JWTFactory::class,
]
```

- 发布配置

> 1. 运行以下命令以发布包配置文件：
> 2. 您现在应该有一个`config/jwt.php`文件，允许您配置此包的基础知识。

```php
php artisan vendor:publish --provider="Tymon\JWTAuth\Providers\LaravelServiceProvider"
```



- 生成密钥

> 1. 我已经包含了一个帮助命令来为你生成一个密钥：
> 2. 这将`.env`使用类似的内容更新您的文件`JWT_SECRET=foobar`
> 3. 这是将用于签署您的令牌的密钥。这究竟如何发生将取决于您选择使用的算法。

```php
php artisan jwt:secret
# 上诉命令会在.env中生成
JWT_SECRET=mNkE9Rba3lH0LxvaHFu6Mx0H6I37JXP4nLW1KI3vVCjuaIwBOyib3QLgjGCFrufz
```

- 在auth.php 文件中 配置 auth guard 让api的driver使用jwt

> 在该`config/auth.php`文件中，您需要进行一些更改以配置 Laravel 以使用`jwt`防护来支持您的应用程序身份验证。

```shell
'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'jwt', # 更改此处为jwt
            'provider' => 'users',
             #'hash' => false,
        ],
    ],
```



- 更新您的用户模型使其支持jwt

> 首先，您需要`Tymon\JWTAuth\Contracts\JWTSubject`在 User 模型上实现合约，这需要您实现 2 个方法`getJWTIdentifier()`和`getJWTCustomClaims()`.

```php
<?php

namespace App;

use Tymon\JWTAuth\Contracts\JWTSubject;
use Illuminate\Notifications\Notifiable;
use Illuminate\Foundation\Auth\User as Authenticatable;

class User extends Authenticatable implements JWTSubject
{
    use Notifiable;

    // 为简洁起见，省略其余部分

    /**
     * 获取将存储在JWT主题声明中的标识符。
     *
     * @return mixed
     */
    public function getJWTIdentifier()
    {
        return $this->getKey();
    }

    /**
     * 返回一个键值数组，其中包含要添加到JWT的任何自定义声明。
     *
     * @return array
     */
    public function getJWTCustomClaims()
    {
        return []; # 这里可以拼接返回信息例如(返回过期时间,用户id,m)  return ['exp'=>7200,'id'=>1,'model'=>$this->->getTable()]
    }
}
```

## 认证方式

> 支持以下两种方式。通过 HTTP 请求来认证：

1. 在headers 中添加Authorization  值是Bearer+空格+token

```php
Authorization header

Authorization: Bearer eyJhbGciOiJIUzI1NiI...   
```

![1627627501(1).jpg](https://i.loli.net/2021/07/30/ahEZCYBNiKgFLTj.png)



  2 .  以参数的格式传输token

```shell
Query string parameter

http://example.dev/me?token=eyJhbGciOiJIUzI1NiI...
```

![1627627721(1).jpg](https://i.loli.net/2021/07/30/kEewtNi273UpHvC.png)

## 代码中使用详解

>创建登录路由

```shell
# 创建登录路由
Route::any('login',[\App\Http\Controllers\AuthUserController::class,'login']);
# 对应控制器代码
 public function login(Request $request)
    {
        # 第二种加密方式 
        # use Tymon\JWTAuth\Facades\JWTAuth;
        # JWTAuth::fromuser(User::where(['email'=>$request->email])->first())
        $token = Auth('api')->attempt($request->all()); # 采用Auth的attempt验证 密码一定要bcrypt()函数加密
        if (!$token){
            return response()->json(['error' => 'Unauthorized'], 401);
        }
        return $this->respondWithToken($token);
    }
   # 组装成功返回的token
    public function respondWithToken($token)
    {
        return response()->json([
            'access_token' => $token,
            'token_type' => 'Bearer',
            'expires_in' => auth('api')->factory()->getTTL() * 60
        ]);
    }  
   # 刷新token
   public function refresh()
   {
        return $this->respondWithToken(auth('api')->refresh());
   }
```

- 获取已经登录的用户

```shell
use Tymon\JWTAuth\Facades\JWTAuth
# 方法一
 $user=JWTAuth::parseToken()->authenticate()->toArray();
# 或
 $user=JWTAuth::parseToken()->touser();
# 方法二 添加 auth:api之后就可以直接通过request->user()获取当前已经登录的用户实例
Route::middleware('auth:api')->get('/user', function (Request $request) {
    return $request->user();
});
# 方法三 
auth()->user();
```

- 退出

 ```shell
 JWTAuth::parseToken()->invalidate()
 ```



- 登录之后访问其他路由

 ```php
 # 例如测试路由,后面加一个中间件
 Route::any('test', [\App\Http\Controllers\TestController::class, 'test'])->middleware('auth:api');
 # 我们也可以使用jwt自带的中间件
 在Http\Kernel.php中的$routeMiddleware中定义jwt提供的中间件
 protected $routeMiddleware = [
     'auth.jwt' => \Tymon\JWTAuth\Http\Middleware\Authenticate::class,
 ]
 ```

## 多表认证

如有需要[参考第三方博客](https://blog.csdn.net/qq_25991751/article/details/114574514)

- 在auth.php 文件中 配置 auth guard 让api的driver使用jwt

> 在该`config/auth.php`文件中，您需要进行一些更改以配置 Laravel 以使用`jwt`防护来支持您的应用程序身份验证。

```shell
'guards' => [
        'web' => [
            'driver' => 'session',
            'provider' => 'users',
        ],

        'api' => [
            'driver' => 'jwt', # 更改此处为jwt
            'provider' => 'users',
             #'hash' => false,
        ],
        # 多指定一个验证
        'admin' => [
            'driver' => 'jwt', # 更改此处为jwt
            'provider' => 'admin',
             #'hash' => false,
        ],
    ],
```

**获取或验证用户信息**

```shell
Auth('admin')->attempt($request->all()); # 采用Auth的attempt验证 密码一定要bcrypt()函数加密

auth('admin')->user(); # 获取用户信息
```

# [firebase/](http://packagist.p2hp.com/packages/firebase/)php-jwt(推荐使用)

> 毕竟jwt官网排行第一的包所以推荐使用

**资料**

| 名称                                                         | 地址                                                        |
| ------------------------------------------------------------ | ----------------------------------------------------------- |
| [firebase/](http://packagist.p2hp.com/packages/firebase/)php-jwt | [link](http://packagist.p2hp.com/packages/firebase/php-jwt) |
| 第三方博客参考                                               | [link ](https://www.cnblogs.com/mg007/p/11293939.html)      |

## 安装

```php
composer require firebase/php-jwt
```

## 代码示例

**登录接口**

> `iss`   token的发行者((issuer)是签发该证书的负责人)
>
> `sub`  token的题目  (Subject)是主体。
>
> `aud` token的客户 (Audience) Claim是指jwt的接受者，假如aud没有发现，则解析jwt时会抛出异常
>
> `exp`  经常使用的，以数字时间定义失效期，也就是当前时间以后的某个时间本token失效。 什么时候过期，这里是一个Unix时间戳，是否使用是可选的（**不使用(隐藏此字段)则生成的token是永久有效的**）
>
> `nbf`定义在此时间之前，JWT不会接受处理。开始生效时间 如果当前时间在nbf里的时间之前，则Token不被接受；一般都会留一些余地，比如几分钟；是否使用是可选的；(Expiration Time指的是过期时间，假如超过过期时间，则会抛出异常)
>
> `iat` JWT发布时间，能用于决定JWT年龄 在什么时候签发的(UNIX时间)，是否使用是可选的(Not Before指的是开始日期，claim要求当前日期/时间必须在以后或等于在“nbf”声明中列出的日期/时间) (Issued At) Claim是指jwt的发行时间；
>
> `jti` JWT唯一标识. 能用于防止 JWT重复使用，一次只用一个token；如果签发的时候这个claim的值是“1”，验证的时候如果这个claim的值不是“1”就属于验证失败(JWT IDJWT提供了惟一的标识符，如果应用程序使用多个发行者，必须在值之间避免冲突，由不同的发行商制作。)

```php
use Illuminate\Http\Request;
use Firebase\JWT\JWT;
Public function authLogin(Request $request){
      // 用户登录逻辑-请自己补充
        $user = Admin::first();
        $userId = $user->admin_id;
        $modelName = $user->getTable();
        $jwt = $this->getResponseJwtEncode($userId, $modelName);
        return response()->json(['code' => '2000', 'msg' => 'success', 'data' => $jwt]);
}

 /**
     * 返回jwt 加密信息
     * $data 需要传递的数据
     * @param integer $id 用户主键
     * @param string $modelName 模型名称
     */
    public function getResponseJwtEncode(int $id, string $modelName)
    {
        $key = "example_key"; # 密钥可以自定义
        $time = time();
        $payload = array(
            "iss" => "http://example.org", # 可以指定程序的域名
            "aud" => "http://example.com",
            "iat" => $time, # 程序的发布时间
            "nbf" => $time, # token生效时间
            "exp" => $time + 7200, # 过期时间两小时
            'id' => $id,
            'model_name' => $modelName
        );
        /*
         * alg 签名算法 HS256
         */
        $jwt = JWT::encode($payload, $key, 'HS256');
        return $jwt;
    }

```

# 拓展

## jwt 单点登录的问题

**说明**

> jwt虽然好用但是有一个缺陷很明显,那就是生成的***jwtToken***在过期时间内都会有效，例如我后台使用了jwt登录，我这个浏览器
>
> 登录了一个账号，我再另一个浏览器又重新登录，这是不行的，这里参考了一个前辈的解决方案
>
> **前辈解决方案**
>
> 用户登录的时候记录一份**md5($jwtToken);**保存到数据库中的字段里，然后用户请求接口的时候会经过中间件校验,
>
> 这里再将用户携带过来的**jwtToken**经过**md5**加密与数据库中(**建议保存在redis hash中**)的字段比较如果相等说明请求过来的是当前登录的用户，
>
> 给予通过，反之抛出**该账号已经在其它地方登录,请重新登录**的错误，前端可以依据错误码进行判断自动退出的操作

## jwt续签问题

> 场景:
>
> 使用jwt有这么一个缺点就是用户在使用我们的程序的时候突然间要跳转到登陆页面了但是用户一直在使用程序，这个时候体验非常不友好
>
> 我们要做到无感知续签问题,只要用户一直在使用我们就需要延长token的有效期

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 参考资料 | [link](https://blog.csdn.net/m0_71777195/article/details/127299554) [link](https://mp.weixin.qq.com/s/9T3_XsKukWlBhbtThof_OQ) |

**续签方案**

> 1. 最简单的一种方式是每次请求刷新JWT，即每个HTTP请求都返回一个新的JWT。这个方法不仅暴力不优雅，而且每次请求都要做JWT的加密解密，会带来性能问题。另一种方法是在redis中单独为每个JWT设置过期时间，每次访问时刷新JWT的过期时间