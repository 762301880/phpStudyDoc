# 资料



| 名称                    | 官网                                                         |
| ----------------------- | ------------------------------------------------------------ |
| github-Guzzle客户端官网 | [link](https://github.com/guzzle/guzzle)                     |
| 官方文档地址            | [link](https://docs.guzzlephp.org/en/stable/)                |
| Guzzle-6中文文档        | [link](https://guzzle-cn.readthedocs.io/zh-cn/latest/quickstart.html) |

## 介绍

> Guzzle 是一个 PHP HTTP 客户端，可以轻松发送 HTTP 请求并轻松地与 Web 服务集成。
>
> - 用于构建查询字符串、POST 请求、流式传输大型上传、流式传输大型下载、使用 HTTP cookie、上传 JSON 数据等的简单界面...
> - 可以使用相同的接口发送同步和异步请求。
> - 使用 PSR-7 接口进行请求、响应和流。这允许您将其他兼容 PSR-7 的库与 Guzzle 结合使用。
> - 支持 PSR-18 允许与其他 PSR-18 HTTP 客户端进行互操作。
> - 抽象出底层的 HTTP 传输，允许您编写与环境和传输无关的代码；即，不硬依赖 cURL、PHP 流、套接字或非阻塞事件循环。
> - 中间件系统允许您增强和组合客户端行为。

```php
$client = new \GuzzleHttp\Client();
$response = $client->request('GET', 'https://api.github.com/repos/guzzle/guzzle');

echo $response->getStatusCode(); // 200
echo $response->getHeaderLine('content-type'); // 'application/json; charset=utf8'
echo $response->getBody(); // '{"id": 1420053, "name": "guzzle", ...}'

// Send an asynchronous request.
$request = new \GuzzleHttp\Psr7\Request('GET', 'http://httpbin.org');
$promise = $client->sendAsync($request)->then(function ($response) {
    echo 'I completed! ' . $response->getBody();
});

$promise->wait();


# post请求

$response = $client->request('POST', 'http://httpbin.org/post', [
    'form_params' => [
        'field_name' => 'abc',
        'other_field' => '123',
        'nested_field' => [
            'nested' => 'hello'
        ]
    ]
]);
```

