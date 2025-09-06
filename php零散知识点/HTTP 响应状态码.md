#  资料

| 名称           | 资料                                                         |
| -------------- | ------------------------------------------------------------ |
| 状态码使用网站 | [link](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Status)   [link](https://www.iana.org/assignments/http-status-codes/http-status-codes.xhtml) |



在 API 开发中，选择合适的状态码对于确保客户端能够正确理解服务器响应至关重要。以下是一些常见 HTTP 状态码及其适用场景：

```shell
200  请求成功状态
400  超文本传输协议（HTTP）400 Bad Request 响应状态码表示服务器因某些被认为是客户端错误的原因（例如，请求语法错误、无效请求消息格式或者欺骗性请求路由），而无法或不会处理该请求。 
```



# 状态码封装

如果你用 PHP 8.1 及以上，推荐用 **枚举 Enum**：

```php+HTML
<?php

enum ResponseCode: int
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
$code = ResponseCode::SUCCESS;
echo $code->value;    // 200
echo $code->message(); // 请求成功
```

## php7.*封装

```php
<?php

class ResponseCode
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

# 对应json返回封装

```php
<?php

class ResponseCode
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

class JsonResponse
{
    /**
     * 统一返回 JSON
     *
     * @param int   $code    状态码
     * @param mixed $data    返回数据（数组/对象/字符串）
     * @param string|null $message 自定义提示信息
     * @return string JSON
     */
    public static function make(int $code, $data = null, string $message = null): string
    {
        $response = [
            'code'    => $code,
            'message' => $message ?? ResponseCode::message($code),
            'data'    => $data,
        ];

        // 设置响应头
        header('Content-Type: application/json; charset=utf-8');
        http_response_code($code);

        return json_encode($response, JSON_UNESCAPED_UNICODE);
    }

    // 快捷方法
    public static function success($data = null, string $message = null): string
    {
        return self::make(ResponseCode::SUCCESS, $data, $message);
    }

    public static function error(int $code = ResponseCode::SERVER_ERROR, string $message = null, $data = null): string
    {
        return self::make($code, $data, $message);
    }
}
```

**使用示例**

```php
// 成功返回
echo JsonResponse::success(['id' => 1, 'name' => '张三']);

// 自定义成功信息
echo JsonResponse::success(['token' => 'xxxx'], '登录成功');

// 失败返回
echo JsonResponse::error(ResponseCode::BAD_REQUEST, '参数缺失');
```

**返回结果示例：**

```php
{
  "code": 200,
  "message": "请求成功",
  "data": {
    "id": 1,
    "name": "张三"
  }
}
```

