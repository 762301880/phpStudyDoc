## [Guzzle HTTP 客户端](https://www.hyperf.wiki/3.1/#/zh-cn/guzzle?id=guzzle-http-客户端)

##  安装

```php
composer require hyperf/guzzle
```



## 代码示例

###  简单先用起来

```php
<?php
declare(strict_types=1);

namespace App\Controller;

use Hyperf\Guzzle\ClientFactory;

class TestController extends AbstractController
{
    // 1. 先声明一个私有属性存客户端工厂
    private ClientFactory $clientFactory;

    // 2. 构造注入，把工厂存起来
    public function __construct(ClientFactory $clientFactory)
    {
        $this->clientFactory = $clientFactory;
    }

    public function test()
    {
        // $options 等同于 GuzzleHttp\Client 构造函数的 $config 参数
        $options = [];
        // 3. 用工厂 -> 创建 Guzzle 客户端
        $client = $this->clientFactory->create($options);
        $res = $client->get('https://httpbin.org/get', [
            'query' => ['name' => 'test']
        ]);

        return $res->getBody()->getContents();
    }
}
```

**返回**

```json
{
  "args": {
    "name": "test"
  }, 
  "headers": {
    "Host": "httpbin.org", 
    "User-Agent": "GuzzleHttp/7", 
    "X-Amzn-Trace-Id": "Root=1-6a0051bd-10b6a9d21c8537583576c96e"
  }, 
  "origin": "47.107.33.56", 
  "url": "https://httpbin.org/get?name=test"
}
```

