# Redis 原子性如何保持

## 什么是原子性

原子性 = 一个操作(要么全部成功执行，要么完全不执行，中间不会被打断、不会一半成功一半失败)

就像转账：扣款 + 入账必须同时成，同时败，不能只扣钱不入账。

##  Redis 为什么天生很多操作是原子

1. Redis 单线程模型（核心原因）

   Redis 处理命令主线程串行执行：

   同一时刻只会执行一条完整命令，不会并发穿插打断。

   所以：

- `set key val`

- `incr count`

- hset hash k v

  这些单个原生命令，天然绝对原子。

1. 多命令默认 

   不是原子

   比如你写两条普通命令：

```shell
set a 1
set b 2
```

两条之间会被其他客户端命令插队，**不保证原子**。

##  常见场景

### 1. 单个命令：100% 原子 ✅

计数器超好用：

```shell
incr user_view
```

高并发秒杀、统计点赞不会超卖、不会脏数据，因为底层单线程串行。

### 2. 多条普通命令：非原子 ❌

```shell
decr stock
incr order_num
```

并发下可能：库存扣了，订单没加 → 数据不一致。

### 3. 多条命令实现原子的 3 种方案（PHP / 后端常用）

#### 方案 1：Lua 脚本（企业最常用）

Lua 里所有 Redis 命令**整体原子执行**，中途不被打断。

示例伪代码：

```lua
if redis.call('get','stock') >= 1 then
    redis.call('decr','stock')
    return 1
else
    return 0
end
```

Hyperf/Laravel 秒杀、分布式锁全靠它。

#### 方案 2：Redis 事务 multi/exec

```shell
multi
decr stock
incr order_num
exec
```

⚠️注意：Redis 事务**不支持回滚**，报错不会撤销，只是打包执行，容错弱，生产少用。

#### 方案 3：Pipeline

只是批量发送网络请求，**不保证原子**，仅提速，别混淆。

## 面试高频总结（背下来）

1. Redis 单命令因**单线程串行**天然原子；
2. 多普通命令非原子，会并发穿插；
3. 强一致性原子优先用 **Lua 脚本**；
4. multi 事务无回滚，可靠性差；pipeline 只优化网络，无原子性。

## PHP 开发实际用途

- 秒杀防超卖
- 分布式锁加解锁原子
- 库存、积分扣减一致性
- 限流计数器安全自增

## 补充

### larave  Redis Lua 原子操作 完整示例（秒杀 / 扣库存 / 防超卖）

核心说明

1. **Lua 脚本在 Redis 中是原子执行**，全程不被其他命令打断
2. 适合：扣库存、减余额、下单、点赞、限流等**必须一致**的场景
3. Laravel 内置 Redis 支持 `eval` 执行 Lua，无需额外扩展

####  最常用：原子扣库存（秒杀专用）

场景

- 商品库存存在 Redis：`goods_stock_1001`
- 要求：**库存足够才扣减，否则返回失败，全程原子**

```php
use Illuminate\Support\Facades\Redis;

/**
 * 原子扣减库存（Lua 保证绝对安全，不超卖）
 * @param int $goodsId 商品ID
 * @param int $num 扣减数量（默认1）
 * @return bool 成功true / 失败false
 */
function deductStock($goodsId, $num = 1)
{
    // 库存 key
    $stockKey = "goods_stock_{$goodsId}";

    // Lua 脚本
    $lua = <<<LUA
        -- 获取当前库存
        local stock = tonumber(redis.call('get', KEYS[1]) or 0)
        -- 扣减数量
        local deductNum = tonumber(ARGV[1])

        -- 判断库存是否足够
        if stock >= deductNum then
            -- 扣减库存
            redis.call('decrby', KEYS[1], deductNum)
            return 1 -- 成功
        else
            return 0 -- 库存不足
        end
    LUA;

    // 执行 Lua：1=key 数量，KEYS[1]，ARGV[1]=扣减数量
    $result = Redis::eval($lua, 1, $stockKey, $num);

    return $result === 1;
}
```

使用方式

```php
// 扣减商品 1001 的库存 1 个
$success = deductStock(1001, 1);

if ($success) {
    echo "扣减成功，创建订单...";
} else {
    echo "库存不足";
}
```

#### 进阶：原子扣库存 + 增加订单数（双操作一致）

场景

- 扣库存 `goods_stock`
- 同时增加订单数 `order_count`
- **两个操作必须同时成功 / 同时失败**

```php
function deductStockAndAddOrder($goodsId, $num = 1)
{
    $stockKey = "goods_stock_{$goodsId}";
    $orderKey = "order_count_{$goodsId}";

    $lua = <<<LUA
        local stock = tonumber(redis.call('get', KEYS[1]) or 0)
        local deductNum = tonumber(ARGV[1])

        if stock >= deductNum then
            redis.call('decrby', KEYS[1], deductNum) -- 扣库存
            redis.call('incrby', KEYS[2], deductNum) -- 加订单
            return 1
        else
            return 0
        end
    LUA;

    // 2 个 key：库存key、订单key
    $result = Redis::eval($lua, 2, $stockKey, $orderKey, $num);

    return $result === 1;
}
```

#### 原子加锁（分布式锁，防止重复提交）

```php
// 获取锁：key=锁名，过期时间=秒，唯一请求ID防止误删
function lock($key, $expire = 10)
{
    $lua = <<<LUA
        if redis.call('setnx', KEYS[1], ARGV[1]) == 1 then
            redis.call('expire', KEYS[1], ARGV[2])
            return 1
        else
            return 0
        end
    LUA;

    $requestId = uniqid(); // 唯一值
    return Redis::eval($lua, 1, $key, $requestId, $expire) === 1;
}
```

#### 为什么 Lua 一定保证原子性？

- Redis **单线程执行 Lua 脚本**
- 执行期间**不会插入任何其他命令**
- 要么全部执行成功，要么全部不执行
- 高并发下**100% 不会超卖、不会数据错乱**

#### 总结

1. **Laravel + Redis Lua** 是高并发一致性操作的**最优方案**
2. 秒杀、库存、余额、订单、锁都必须用这种方式