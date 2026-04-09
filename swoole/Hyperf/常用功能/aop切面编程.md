## 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| [官方文档] | [AOP 面向切面编程](https://www.hyperf.wiki/2.2/#/zh-cn/aop?id=aop-面向切面编程) |

## 代码实践

### **切面类**

```php
<?php

declare(strict_types=1);

namespace App\Aspect;

use App\Controller\TestController;
use App\Service\DemoService;
use Hyperf\Di\Aop\ProceedingJoinPoint;
use Hyperf\Di\Aop\AbstractAspect;
use Hyperf\Di\Annotation\Aspect;


/**
 * @Aspect
 */
class TimeAspect extends AbstractAspect
{
    public $classes = [
//        DemoService::class,
        'App\Controller\TestController::test'
    ];

    //要切入的注解，具体切入的还是使用了这些注解的类，仅可切入类注解和类方法注解
    public $annotations = [
    ];
    public function process(ProceedingJoinPoint $proceedingJoinPoint)
    {
        echo "开始切入";

        $start = microtime(true);

        $result = $proceedingJoinPoint->process();

        $end = microtime(true);
        $time = round(($end - $start) * 1000, 2);

        echo "方法 {$proceedingJoinPoint->className}::{$proceedingJoinPoint->methodName} 执行时间: {$time} ms\n";

        return $result;
    }
}

```

### 控制器类

```php
<?php
declare(strict_types=1);

namespace App\Controller;


use App\Service\DemoService;

class TestController extends AbstractController
{
    public function test()
    {
        return 'test2';
    }
}
```

### 注册aop

config\autoload\aspects.php

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
return [
    \App\Aspect\TimeAspect::class
];

```



以上调用控制器 时候可以在

```php
php bin/hyperf.php start  # 启动的控制台里看到调用的切面输出信息
    
# 例如
    
[INFO] Worker#1 started.
[INFO] HTTP Server listening at 0.0.0.0:9501
[DEBUG] Event Hyperf\Framework\Event\OnManagerStart handled by Hyperf\Server\Listener\InitProcessTitleListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\Server\Listener\AfterWorkerStartListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\Server\Listener\AfterWorkerStartListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\WebSocketServer\Listener\InitSenderListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\WebSocketServer\Listener\InitSenderListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\Server\Listener\InitProcessTitleListener listener.
[DEBUG] Event Hyperf\Framework\Event\AfterWorkerStart handled by Hyperf\Server\Listener\InitProcessTitleListener listener.
开始切入aa方法 App\Controller\TestController::test 执行时间: 0 ms    
```



