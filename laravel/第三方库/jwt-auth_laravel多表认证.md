# 说明&资料

## 说明

> [此段说明截取第三方博客](https://laravelacademy.org/post/9794

### 什么是jwt

> jwt单词代表的意思就是JSON Web Token。
>
> 我们在做api请求的时候，通常要使用token，来验证是否这个请求能不能访问。
>
> 一旦用户登录，后续每个请求都将包含JWT，允许用户访问该令牌允许的路由、服务和资源。单点登录是现在广泛使用的JWT的一个特性，因为它的开销很小，并且可以轻松地跨域使用。

### 为什么使用jwt

> Session是在服务器端的，而JWT是在客户端的。
>
> Session方式存储用户信息的最大问题在于要占用大量服务器内存，增加服务器的开销，而JWT方式将用户状态分散到了客户端中，可以明显减轻服务端的内存压力。



## 资料

| name                      | url                                                          |
| ------------------------- | ------------------------------------------------------------ |
| 第三方博客                | [link](https://learnku.com/articles/30342)                   |
| jwt-auth Wiki文档         | [link](https://jwt-auth.readthedocs.io/en/develop/laravel-installation/) |
| packagist项目地址         | [link](https://packagist.org/packages/tymon/jwt-auth)        |
| jwt超详细解释             | [link](https://learnku.com/articles/17883)                   |
| laravel学院               | [link](https://laravelacademy.org/post/9178)                 |
| laravel-wikis-jwt认证详解 | [link](https://learnku.com/laravel/wikis/25704)              |



# 使用实例

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
        return [];
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



# 多表认证日后补充

如有需要[参考第三方博客](https://blog.csdn.net/qq_25991751/article/details/114574514)

