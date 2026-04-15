#  超级大bug解析

# 参考博客 恩人啊 

https://www.ziruchu.com/art/659

pdf备份 https://yaoliuyang.lanzoul.com/iULfk3nbqm3e



# 终极真相（就这一句）

## **Hyperf 2.2 jsonrpc-http 默认规则：**

- `@RpcService(name="CalculatorService")`
- 会自动生成路由：**`/calculator/add`**
- 路由规则：**小写服务名 / 方法名**

**curl请求**

```bash
curl -v -H "Content-Type: application/json" http://47.107.33.56:9554 -d '{"jsonrpc":"2.0","method":"calculator/add","params":[1,2],"id":1}'
```

**postman调用**

```php
# 请求地址  post  http://47.107.33.56:9554
headers 添加  Content-Typ  application/json
# body中添加 
{
    "jsonrpc": "2.0",
    "method": "/calculator/add",
    "params": [10, 20],
    "id": 1
}
```



**为什么是小写**

> D:\phpstudy_pro\WWW\hyperf_study\vendor\hyperf\rpc\src\PathGenerator\PathGenerator.php
>
> 2.2 里面有 底层源码

```php
<?php

declare(strict_types=1);
/**
 * This file is part of Hyperf.
 *
 * @link     https://www.hyperf.io
 * @document https://hyperf.wiki
 * @contact  group@hyperf.io
 * @license  https://github.com/hyperf/hyperf/blob/master/LICENSE
 */
namespace Hyperf\Rpc\PathGenerator;

use Hyperf\Rpc\Contract\PathGeneratorInterface;
use Hyperf\Utils\Str;

class PathGenerator implements PathGeneratorInterface
{
    public function generate(string $service, string $method): string
    {
        $handledNamespace = explode('\\', $service);
        $handledNamespace = Str::replaceArray('\\', ['/'], end($handledNamespace));
        $handledNamespace = Str::replaceLast('Service', '', $handledNamespace);
        $path = Str::snake($handledNamespace);

        if ($path[0] !== '/') {
            $path = '/' . $path;
        }
        return $path . '/' . $method;
    }
}

```

#### 3. **官方文档（隐藏、缺失、不写）**

- 官方 2.2 文档「JSON-RPC」章节：

  - 只讲「注册中心、服务发现、客户端代理」
  - **完全不提「直接 IP + 端口调用」**
  - **完全不提「小写服务名 / 方法名」**

  

- 官方 3.x 文档：

  - 改成 `服务名::方法名`（如 `CalculatorService::add`）
  - 文档混写，**不区分 2.2 / 3.x**

  

------

### 二、为什么官方文档不写？（真相）

1. **文档面向「分布式微服务」**

   

   - 默认假设你用 Nacos/Consul
   - 不提「单机直接调用」这种最简场景

   

2. **2.2 文档「严重缺失」**

   

   - 很多「默认行为」只在源码里
   - 官方没补全、没写清楚

   

3. **版本混乱（2.2 vs 3.x）**

   

   - 2.2：`calculator/add`（小写 + 斜杠）
   - 3.x：`CalculatorService::add`（:: 分隔）
   - 文档混在一起，**新手必被坑**

   