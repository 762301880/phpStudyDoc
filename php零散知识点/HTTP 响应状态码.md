#  资料

| 名称               | 资料                                                         |
| ------------------ | ------------------------------------------------------------ |
| 状态码使用网站     | [link](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)   [link](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) |
| 菜鸟教程http状态码 | [link]()                                                     |



在 API 开发中，选择合适的状态码对于确保客户端能够正确理解服务器响应至关重要。以下是一些常见 HTTP 状态码及其适用场景：

```shell
200  请求成功状态
400  超文本传输协议（HTTP）400 Bad Request 响应状态码表示服务器因某些被认为是客户端错误的原因（例如，请求语法错误、无效请求消息格式或者欺骗性请求路由），而无法或不会处理该请求。 
```



# 状态码封装

## PHP 8.1 及以上，推荐用 **枚举 Enum**：

```php+HTML
<?php

enum HttpCodeEnum: int
{
    case SUCCESS = 200;
    case BAD_REQUEST = 400;
    case UNAUTHORIZED = 401;
    case FORBIDDEN = 403;
    case NOT_FOUND = 404;
    case SERVER_ERROR = 500;

    public function message(): string
    {
        return match($this) {
            self::SUCCESS       => '请求成功',
            self::BAD_REQUEST   => '请求参数错误',
            self::UNAUTHORIZED  => '未授权',
            self::FORBIDDEN     => '禁止访问',
            self::NOT_FOUND     => '资源未找到',
            self::SERVER_ERROR  => '服务器错误',
        };
    }
}

// 使用示例：
$code = HttpCodeEnum::SUCCESS;
echo $code->value;    // 200
echo $code->message(); // 请求成功
```

## php7.*封装

```php
<?php

namespace App\Enums;

class HttpCodeEnum
{
    const SUCCESS       = 200;
    const BAD_REQUEST   = 400;
    const UNAUTHORIZED  = 401;
    const FORBIDDEN     = 403;
    const NOT_FOUND     = 404;
    const SERVER_ERROR  = 500;

    public static function message(int $code): string
    {
        $map = [
            self::SUCCESS       => '请求成功',
            self::BAD_REQUEST   => '请求参数错误',
            self::UNAUTHORIZED  => '未授权',
            self::FORBIDDEN     => '禁止访问',
            self::NOT_FOUND     => '资源未找到',
            self::SERVER_ERROR  => '服务器错误',
        ];

        return $map[$code] ?? '未知错误';
    }
}

// 使用示例：
echo ResponseCode::SUCCESS; // 200
echo ResponseCode::message(ResponseCode::SUCCESS); // 请求成功
```

#  laravel统一封装成功失败返回

> 大厂通常都用 **0** 当返回成功 参考小红书
>
> 自定义失败从 code**1000** 开始

1. **成功**：`code: 0`，HTTP 200
2. **业务错误**：`code: 1000+`，HTTP 400
3. **未登录**：`code: 1001`，HTTP 401
4. **无权限**：`code: 1003`，HTTP 403
5. **服务器错误**：`code: 9999`，HTTP 500（系统自动抛出）

## 封装个人业务状态码区分

> 区分：
>
> - HTTP 状态码（协议层）
> - 业务状态码（业务层）
>
> 这两个本来就不应该混用。

```php
<?php
namespace App\Enums;

class BusinessCodeEnum
{
    const SUCCESS = 0;
    const BAD_REQUEST = 1000;
    const USER_NAME_NULL=1001;//自定义业务码 

    public static function message(int $code): string
    {
        $map = [
            self::SUCCESS => '操作成功',
            self::BAD_REQUEST => '操作失败',
            self::USER_NAME_NULL => '用户名不存在',
        ];

        return $map[$code] ?? '未知错误';
    }
}
```

### 为什么不能把业务码也写成 200

HTTP 已经返回：

```
HTTP/1.1 200 OK
```

这代表：

> 请求成功到达服务器，服务器也正常处理并返回了响应。

这里的 `200` 是：

- HTTP协议层成功
- 不是业务成功

如果你 JSON 里面再写：

```json
{
    "code": 200
}
```

就会出现语义混乱：

到底：

- 是 HTTP 200？
- 还是业务成功？

所以业内通常：

```php
SUCCESS = 0
# 或者：
SUCCESS = 10000
# 而不是再用 200。    
```





## **示例代码**

```php
<?php

namespace App\Traits;

use App\Enums\BusinessCodeEnum;
use App\Enums\HttpCodeEnum;

trait ApplyResponseLayout
{
    /**
     * 成功响应
     * @param string $msg 提示信息
     * @param array $data 返回数据
     * @param int $httpCode HTTP 状态码
     * @return \Illuminate\Http\JsonResponse
     */
    protected function success($msg = '操作成功', $data = [], $httpCode = HttpCodeEnum::SUCCESS)
    {
        return response()->json([
            'code' => BusinessCodeEnum::SUCCESS,       // 业务码：0 = 成功
            'msg' => $msg,
            'data' => $data
        ], $httpCode);
    }

    /**
     * 错误响应
     * @param string $msg 错误信息
     * @param int $businessCode 业务错误码
     * @param int $httpCode HTTP 状态码
     * @return \Illuminate\Http\JsonResponse
     */
    protected function error($msg = '操作失败', $businessCode = BusinessCodeEnum::BAD_REQUEST, $httpCode = HttpCodeEnum::BAD_REQUEST)
    {
        return response()->json([
            'code' => $businessCode, // 业务错误码（前端判断用）
            'msg' => $msg,
            'data' => null
        ], $httpCode);
    }
}
```

