

## 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| [官方文档] | [AOP 面向切面编程](https://www.hyperf.wiki/2.2/#/zh-cn/aop?id=aop-面向切面编程) |

## 讲解

AOP 为 `Aspect Oriented Programming` 的缩写，意为：`面向切面编程`，通过动态代理等技术实现程序功能的统一维护的一种技术。AOP 是 OOP 的延续，也是 Hyperf 中的一个重要内容，是函数式编程的一种衍生范型。利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

用通俗的话来讲，就是在 Hyperf 里可以通过 `切面(Aspect)` 介入到任意类的任意方法的执行流程中去，从而改变或加强原方法的功能，这就是 AOP。

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



#  真实场景举例

Hyperf AOP 最核心的价值是**在不修改业务代码的前提下，为类 / 方法统一插入横切逻辑**（日志、事务、权限、缓存、限流等）。下面给你 **8 个真实项目常用场景 + 可直接复制的最简代码示例**（都是线上项目常用写法）。

------

## 1. 统一业务日志（最常用）

**场景**：自动记录所有 Service 层的入参、出参、执行时间、异常，用于排查 / 审计。

**真实用途**：用户注册、下单、支付、退款等关键行为全链路日志。

```php
// 1. 切面：App\Aspect\LogAspect.php
#[Aspect]
class LogAspect extends AbstractAspect
{
    // 切入所有 Service 类的所有方法
    public array $classes = [
        'App\Service\*Service::*',
    ];

    public function process(ProceedingJoinPoint $point)
    {
        $start = microtime(true);
        $className = $point->getClassName();
        $methodName = $point->getMethodName();
        $params = $point->getArguments(); // 方法入参

        try {
            $result = $point->process(); // 执行原方法
            // 成功日志
            $this->logInfo($className, $methodName, $params, $result, microtime(true)-$start);
            return $result;
        } catch (Throwable $e) {
            // 异常日志
            $this->logError($className, $methodName, $params, $e, microtime(true)-$start);
            throw $e; // 继续抛出异常
        }
    }
}
```

------

## 2. 声明式事务（替代手动 begin/commit）

**场景**：给方法加注解自动开启 / 提交事务，异常自动回滚。

**真实用途**：下单（扣库存 + 创建订单 + 扣余额）、转账等强事务场景。

```php
// 1. 注解：App\Annotation\Transactional
#[Attribute(Attribute::TARGET_METHOD)]
class Transactional extends AbstractAnnotation {}

// 2. 切面：TransactionalAspect
#[Aspect]
class TransactionalAspect extends AbstractAspect
{
    public array $annotations = [Transactional::class];

    public function process(ProceedingJoinPoint $point)
    {
        $connection = Container::get()->get(DB::class)->connection();
        try {
            $connection->beginTransaction();
            $result = $point->process();
            $connection->commit();
            return $result;
        } catch (Throwable $e) {
            $connection->rollBack();
            throw $e;
        }
    }
}

// 3. 使用（业务代码零侵入）
class OrderService
{
    #[Transactional] // 自动事务
    public function createOrder($userId, $skuId, $num)
    {
        // 扣库存
        // 创建订单
        // 扣余额
    }
}
```

------

## 3. 权限 / 菜单校验（API / 服务层鉴权）

**场景**：方法执行前统一校验用户是否有该权限 / 角色。

**真实用途**：后台管理系统（删除用户、修改配置、查看财务数据）。

```php
// 1. 权限注解
#[Attribute(Attribute::TARGET_METHOD)]
class Permission extends AbstractAnnotation
{
    public string $code;
    public function __construct(string $code) { $this->code = $code; }
}

// 2. 切面
#[Aspect]
class PermissionAspect extends AbstractAspect
{
    public array $annotations = [Permission::class];

    public function process(ProceedingJoinPoint $point)
    {
        $annotation = $point->getAnnotation(Permission::class);
        $userId = auth()->id();
        // 真实：查DB/Redis判断是否有权限
        if (!AuthService::hasPermission($userId, $annotation->code)) {
            throw new BusinessException('无权限访问');
        }
        return $point->process();
    }
}

// 3. 使用
class AdminUserController
{
    #[Permission('user:delete')]
    public function delete($id) { ... }
}
```

------

## 4. 接口限流 & 防重复提交

**场景**：防止恶意刷接口、表单重复提交（秒杀、短信、支付）。

**真实用途**：短信验证码、下单、支付、评论、抽奖。

```php
// 切面：RateLimitAspect
#[Aspect]
class RateLimitAspect extends AbstractAspect
{
    public array $annotations = [RateLimit::class];

    public function process(ProceedingJoinPoint $point)
    {
        $key = 'rate_limit:' . auth()->id() . ':' . $point->getMethodName();
        $redis = Container::get()->get(Redis::class);
        // 10秒内只能调用1次
        if ($redis->get($key)) {
            throw new BusinessException('操作频繁，请稍后再试');
        }
        $redis->setex($key, 10, 1);
        return $point->process();
    }
}
```

------

## 5. 自动缓存（查询结果透明缓存）

**场景**：查询方法自动读缓存，不存在则执行 DB 并写入缓存。

**真实用途**：商品详情、分类列表、用户信息、配置项。

```php
// 切面：CacheAspect
#[Aspect]
class CacheAspect extends AbstractAspect
{
    public array $annotations = [Cacheable::class];

    public function process(ProceedingJoinPoint $point)
    {
        $anno = $point->getAnnotation(Cacheable::class);
        $key = $anno->prefix . ':' . implode(',', $point->getArguments());
        $redis = redis();

        // 读缓存
        if ($data = $redis->get($key)) {
            return unserialize($data);
        }

        // 执行DB
        $result = $point->process();
        // 写缓存
        $redis->setex($key, $anno->ttl, serialize($result));
        return $result;
    }
}

// 使用
class GoodsService
{
    #[Cacheable(prefix: 'goods:detail', ttl: 3600)]
    public function getDetail($goodsId) { ... }
}
```

------

## 6. 数据权限（行级 / 部门级数据隔离）

**场景**：自动在查询前注入 WHERE 条件（只能看自己 / 本部门数据）。

**真实用途**：CRM、ERP、OA、电商多店铺后台。

```php
// 切面：DataScopeAspect
// 切入所有 getList/query 方法
// 自动修改查询参数/Builder，追加 where user_id = ? 或 where dept_id = ?
```

------

## 7. 响应统一包装 & 参数过滤

**场景**：所有接口自动格式化返回、XSS 过滤、空值转 null、脱敏。

**真实用途**：API 标准化、数据安全。

```php
// 切入所有 Controller 方法
// 自动把返回 [data=>...] 包装成 {code:200, data:..., msg:"ok"}
// 自动对入参做 XSS 过滤、trim
```

------

## 8. 分布式锁（秒杀 / 库存超卖控制）

**场景**：方法执行前加 Redis 锁，防止并发超卖。

**真实用途**：秒杀下单、优惠券领取、库存扣减。

```php
// 切面：DistributedLockAspect
// 注解传入锁key，切面自动加锁/解锁
// 失败直接抛异常："操作冲突，请重试"
```

------

## 总结：AOP 适合什么？

- ✅ **横切逻辑**：日志、事务、权限、缓存、限流、监控
- ✅ **非业务核心**：不影响主流程，可插拔
- ✅ **统一管控**：全局规则、安全、审计、标准化
- ❌ **不适合**：业务主逻辑、复杂分支、频繁变更的逻辑