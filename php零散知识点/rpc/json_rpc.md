## 参考资料

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| 网络博客         | [link](https://cloud.tencent.com/developer/article/2654676)  |
| json_rpc官方文档 | [link-中文版](https://wiki.geekdream.com/Specification/json-rpc_2.0.html)     [link-英文版](https://www.jsonrpc.org/specification) |



## JSON-RPC 规范

标准请求示例：

```json
{
    "jsonrpc":"2.0",
    "method":"user.info",
    "params":{"id":1},
    "id":1001
}
```

标准响应：

```json
{
    "jsonrpc":"2.0",
    "result":{"uid":1,"name":"test"},
    "id":1001
}
```

## laravel手搓json_rpc代码

### 路由

```php
// JSON-RPC统一入口
Route::post('json_rpc', [\App\Http\Controllers\JsonRpcController::class, 'handle']);
```

> ⚠️ 重点：api 路由默认**不经过 CSRF 中间件**，如果放到 web.php 必须在 `VerifyCsrfToken` 排除该路由

```php
// app/Http/Middleware/VerifyCsrfToken.php
protected $except = [
    '/json_rpc'
];
```



### 控制器

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Http\Response;
use Illuminate\Http\JsonResponse;
use ReflectionClass;
use ReflectionMethod;

class JsonRpcController extends Controller
{
    // JSON-RPC 标准错误码定义
    /**
     * JSON-RPC 2.0 协议标准错误码
     * @see https://www.jsonrpc.org/specification
     */

    /**
     * 解析错误
     * 服务端无法解析收到的JSON文本，语法错误、格式非法
     */
    private const PARSE_ERROR = -32700;

    /**
     * 无效请求
     * 接收到的JSON不是合法的JSON-RPC请求对象
     */
    private const INVALID_REQUEST = -32600;

    /**
     * 方法不存在
     * 请求指定的 method 在服务端没有定义，找不到对应处理函数
     */
    private const METHOD_NOT_FOUND = -32601;

    /**
     * 参数无效
     * 调用方法传入的参数不合法、缺失必填参数、参数类型不匹配
     */
    private const INVALID_PARAMS = -32602;

    /**
     * 内部错误
     * 服务端执行方法时发生未知异常、程序运行错误
     */
    private const INTERNAL_ERROR = -32603;


    public function handle(Request $request)
    {
        // 读取原始POST数据（不要用 $request->all()，会破坏原始json结构）
        $rawBody = file_get_contents('php://input');
        $payload = json_decode($rawBody, true);
        // 1. JSON解析失败
        if (json_last_error() !== JSON_ERROR_NONE) {
            return $this->jsonResponse([
                'jsonrpc' => '2.0',
                'error' => [
                    'code' => self::PARSE_ERROR,
                    'message' => 'Parse error'
                ],
                'id' => null
            ]);
        }

        // 支持批量请求（数组）
        if (is_array($payload) && array_is_list($payload)) {
            $result = [];
            foreach ($payload as $item) {
                $result[] = $this->processSingleRequest($item);
            }
            return $this->jsonResponse($result);
        }

        // 单次请求处理
        return $this->jsonResponse($this->processSingleRequest($payload));
    }

    /**
     * 处理单条RPC请求
     */
    protected function processSingleRequest(?array $data): array
    {
        // 基础格式校验
        if (!isset($data['jsonrpc'], $data['method']) || $data['jsonrpc'] !== '2.0') {
            return [
                'jsonrpc' => '2.0',
                'error' => [
                    'code' => self::INVALID_REQUEST,
                    'message' => 'Invalid Request'
                ],
                'id' => $data['id'] ?? null
            ];
        }

        $id = $data['id'] ?? null;
        $method = $data['method'];
        $params = $data['params'] ?? [];

        try {
            // 约定格式：method = "服务类.方法名" 例：UserService.getInfo
            [$class, $action] = explode('.', $method, 2);

            // 命名空间（根据你项目目录自行修改）
            $className = "\\App\\Services\\{$class}Service";

            if (!class_exists($className)) {
                throw new \Exception('Method not found');
            }

            $refClass = new ReflectionClass($className);
            if (!$refClass->hasMethod($action)) {
                throw new \Exception('Method not found');
            }

            // Laravel容器自动实例化（支持依赖注入）
            $instance = app($className);
            $refMethod = new ReflectionMethod($instance, $action);

            // 调用方法
            if (is_array($params) && array_is_list($params)) {
                // 索引数组 顺序传参
                $result = $refMethod->invokeArgs($instance, $params);
            } else {
                // 关联数组 命名参数
                $args = [];
                foreach ($refMethod->getParameters() as $param) {
                    $name = $param->getName();
                    if (array_key_exists($name, $params)) {
                        $args[] = $params[$name];
                    } elseif ($param->isDefaultValueAvailable()) {
                        $args[] = $param->getDefaultValue();
                    } else {
                        throw new \Exception('Invalid params');
                    }
                }
                $result = $refMethod->invokeArgs($instance, $args);
            }

            return [
                'jsonrpc' => '2.0',
                'result' => $result,
                'id' => $id
            ];

        } catch (\Exception $e) {
            $errCode = self::INTERNAL_ERROR;
            $msg = $e->getMessage();

            if ($msg === 'Method not found') {
                $errCode = self::METHOD_NOT_FOUND;
            } elseif ($msg === 'Invalid params') {
                $errCode = self::INVALID_PARAMS;
            }

            return [
                'jsonrpc' => '2.0',
                'error' => [
                    'code' => $errCode,
                    'message' => $msg
                ],
                'id' => $id
            ];
        }
    }

    /**
     * 输出标准JSON
     */
    protected function jsonResponse(array $data): JsonResponse
    {
        return response()->json(
            $data,
            200,
            [],
            JSON_UNESCAPED_UNICODE
        );
    }
}
```

### service

```php
<?php


namespace App\Services;


class UserService
{
    /**
     * 命名参数示例
     * method: User.getInfo
     * params: {"uid":1}
     */
    public function getInfo(int $uid)
    {
        return [
            'uid' => $uid,
            'username' => 'test_user',
            'time' => time()
        ];
    }

    /**
     * 顺序参数示例
     * method: User.sum
     * params: [10,20]
     */
    public function sum(int $a, int $b)
    {
        return $a + $b;
    }
}
```

### 客户端调用测试（Postman /curl）

### 请求地址

POST http://127.0.0.1/api/json_rpc

` Header: `Content-Type: application/json

**示例报文 1（命名参数）**

```json
{
    "jsonrpc":"2.0",
    "method":"User.getInfo",
    "params":{"uid":1},
    "id":1001
}
```

返回：

```json
{
    "jsonrpc":"2.0",
    "result":{"uid":1,"username":"test_user","time":1754000000},
    "id":1001
}
```

**示例报文 2（顺序参数）**

```json
{
    "jsonrpc":"2.0",
    "method":"User.sum",
    "params":[10,20],
    "id":1002
}
```

返回:

```json
{
    "jsonrpc": "2.0",
    "result": 30,
    "id": 1002
}
```

### 注意事项

1. **禁止中间件统一格式化返回** 如果项目有全局响应中间件（统一包装 `code/data/msg`），**必须跳过 `/rpc` 路由**，否则破坏 JSON-RPC 标准结构。
2. 异常不要返回 HTML 错误页 生产环境确保 `APP_ENV=production`，Laravel 不会输出异常堆栈 HTML。
3. 安全建议
   - 增加 IP 白名单中间件
   - 增加签名校验（在 params 内携带 sign，自行扩展）
   - 禁止调用危险类 / 方法，可以配置允许调用的服务白名单
4. 批量请求支持 客户端传入数组形式多条请求，服务端会逐条处理并返回数组。

### 可选扩展方向

- 增加方法白名单控制，防止任意类调用
- 增加请求签名鉴权
- 日志记录 RPC 调用、耗时、异常
- 支持通知（不带 id 的 JSON-RPC 请求，不需要返回响应）