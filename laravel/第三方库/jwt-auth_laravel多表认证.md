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

![1627627501(1).jpg](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/ahEZCYBNiKgFLTj.png)



  2 .  以参数的格式传输token

```shell
Query string parameter

http://example.dev/me?token=eyJhbGciOiJIUzI1NiI...
```

![1627627721(1).jpg](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/kEewtNi273UpHvC.png)

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
>
> 2. jwt 有什么续期方案？ JWT本身不提供续期方案，但是可以通过一些外部手段来实现续期。 其中一种常见的手段是使用另一个token来授予请求发起者更新jwt的权限。这种模式通常称为“refresh token”，就像一个访问令牌，可以用来更新jwt。当用户登录到应用程序时，服务器会发出一个refresh token和一个jwt，两者都有自己的有效期限，refresh token通常比jwt有更长的有效期限。在jwt到期之前，用户可以使用refresh token来更新jwt，而不必重新登录。
>
> 3. JWT 续签即在原有 token 的基础上，给它签发一个新 token，同时原有 token 也仍然可用，新 token 也可以单独使用。 实现续签的步骤： 1. 客户端携带原有的 token 向服务器申请续签，服务器验证 token 是否有效； 2. 如果 token 有效，则服务器会根据原有的 token 签发一个新的 token，同时也更新原有 token 的过期时间； 3. 服务器返回新的 token 给客户端； 4. 客户端可以使用新的 token 或者原有的 token 访问服务器。
>
> 4. jwt续签是一种在原有token有效期内，重新计算一个剩余有效期的机制。 通常情况下，jwt续签会在检测到原有token快要过期时，向服务器申请续签，服务器会重新计算一个新的token并返回给用户，而此时原有token仍然处于有效状态，但是新的token会覆盖掉原有token，使用新的token即可。 jwt续签的实现过程一般涉及以下几个步骤： 1、用户在客户端发出请求，并携带原有token； 2、服务器端收到请求，验证token有效性，并计算token的剩余有效期； 3、服务器端生成新的token，覆盖原有token； 4、服务器端将新的token返回给客户端； 5、客户端将新的token保存在本地，并使用新的token进行请求。

**网上代码参考**

- 方案一(不是很好)

```php
php jwt实现续签代码

<?php
/**
 * jwt实现续签
 */

//签发token
function generateToken(){
    //签发者
    $iss = 'www.xxx.com';
    //接收者
    $aud = 'client';
    //签发时间
    $iat = time();
    //过期时间
    $exp = $iat + 60*60;
    //私钥
    $secret = 'xxx';

    $payload = array(
        "iss"=>$iss,
        "aud"=>$aud,
        "iat"=>$iat,
        "exp"=>$exp
    );

    $jwt = JWT::encode($payload, $secret);
    return $jwt;
}

//验证token
function validateToken($jwt){
    $secret = 'xxx';
    try {
        $payload = JWT::decode($jwt, $secret,array('HS256'));
        return true;
    } catch (\Firebase\JWT\ExpiredException $e) {
        return false;
    }
}

//实现续签
function renewToken($jwt){
    $secret = 'xxx';
    try {
        $payload = JWT::decode($jwt, $secret,array('HS256'));
        //重新设置过期时间
        $payload->exp = time() + 60*60;
        $newToken = JWT::encode($payload,$secret);
        return $newToken;
    } catch (\Firebase\JWT\ExpiredException $e) {
        return false;
    }
}

//调用
$token = generateToken();
echo $token;
echo "\n";
$renewToken = renewToken($token);
echo $renewToken;
```

- 方案二

  ```php
  <?php
  
  // jwt续签代码：
  
  // 加载composer库
  require_once __DIR__ . "/vendor/autoload.php";
  
  // 使用firebase/php-jwt库
  use \Firebase\JWT\JWT;
  
  // 获取需要续签的jwt
  $jwt_string = "eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiIxMjM0NTY3ODkwIiwibmFtZSI6IkpvaG4gRG9lIiwiaWF0IjoxNTE2MjM5MDIyfQ.SflKxwRJSMeKKF2QT4fwpMeJf36POk6yJV_adQssw5c";
  
  // 将jwt解码
  $decoded = JWT::decode($jwt_string, 'secret', array('HS256'));
  
  // 将jwt的过期时间延长1天
  $decoded->exp = time() + 86400;
  
  // 生成新的jwt
  $jwt_string_renewed = JWT::encode($decoded, 'secret');
  
  echo $jwt_string_renewed;
  ```

  

### [个人觉得最好的方案](https://blog.csdn.net/qq_27878777/article/details/126765385)

> 首先，refreshToken有效时间一定要比token有效时间长至少才能不影响用户体验，具体要长多少得以实际需求为准，建议是24小时
>
> 前端每次访问后端都携带token，如果token失效则后端直接返回类似token失效请重新登录的报文。
>
> 前端第一次收到token失效的响应后，从本地存储拿refreshToken再去请求
>
> 后端一检测到refreshToken参数不为空，就去校验解析这个refreshToken
>
> 如果有效，后端就返回一个新的token及refreshToken给前端，前端收到后更新本地存储，同时拿这个新的token向后端发起第三次请求，然后成功获取资源
>
> 如果无效，则后端同样返回token失效，前端第二次收到失效的响应则跳转到登录页重新登录
>
> refreshToken比较可以存redis并用md5加密比较

**实现步骤**

> php jwt refresh_token 实现 
>
> 1、生成access_token 通过私钥，使用令牌的相关信息（用户ID，过期时间等）生成一个过期时间短的access_token。
>
>  2、生成refresh_token 通过私钥，使用令牌的相关信息（用户ID，过期时间等）生成一个过期时间长的refresh_token。
>
>  3、实现refresh_token 当access_token过期时，客户端可以通过refresh_token来换取新的access_token。此时，客户端需要携带refresh_token以及相关的信息，发起请求给服务端，服务端根据refresh_token及其相关信息，验证refresh_token的合法性，如果验证成功则生成新的access_token，并且返回给客户端。

**中间件实现逻辑**

```shell
<?php

namespace app\http\middleware;

use app\common\model\AuntModel;
use app\common\service\JwtService;
use constant\ErrorCode;
use exception\SystemException;
use Firebase\JWT\ExpiredException;
use traits\ApplyResponseLayout;

class JwtVerify
{
    use ApplyResponseLayout;

    public function handle($request, \Closure $next)
    {
        try {
            $token = $request->header('Authorization');
            if (empty($token)) throw new SystemException("token不能为空");
            $res = JwtService::validateToken($token);
            $id = $res->id ?? "";
            $model = !empty($res->model_name) ? (new $res->model_name) : "";
            $modelInstance = !empty($model) ? $model->get($id) : "";
            if ($modelInstance instanceof AuntModel) $this->appendReqData($request, $modelInstance);
        } catch (SystemException $systemException) {
            return $this->resError($systemException->getMessage());
        } catch (ExpiredException $exception) {
            return $this->resError($exception->getMessage(), '', ErrorCode::$USER_TOKEN_EXPIRED);
        }
        return $next($request);
    }

    /**
     * 添加附加数据
     * @param $request
     * @param $modelInstance
     */
    public function appendReqData($request, $modelInstance)
    {
        # 构建阿姨返回全局请求数据
        if ($modelInstance instanceof AuntModel) {
            $request->userId = $modelInstance->id ?? ""; //返回用户id
            $request->userPhone = $modelInstance->mobile ?? ""; //手机号码
            $request->userName = $modelInstance->name ?? ""; //用户姓名
        }
    }
}
```

