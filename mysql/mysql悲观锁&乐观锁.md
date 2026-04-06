# 资料

| 名称     | 地址                                                        |
| -------- | ----------------------------------------------------------- |
| 网络博客 | [link](https://cloud.tencent.com/developer/article/1822614) |

#  理解

MySQL 悲观锁与乐观锁 超通俗理解 + SQL 示例

**悲观锁和乐观锁都是解决「多线程 / 多请求同时修改同一条数据」导致的数据错乱问题**，只是思路完全相反。

先记住核心区别：

- **悲观锁**：我修改数据时，**认定别人一定会来抢**，所以先把数据锁死，别人只能等我用完。
- **乐观锁**：我修改数据时，**认定别人大概率不会来抢**，修改时再检查有没有被改动，有冲突就重试。

## 一、悲观锁（Pessimistic Locking）

### 1. 核心理解

**很悲观，总觉得别人要改数据**

- 操作数据前**先加锁**，加锁期间其他人**不能读、不能改**（或只能读不能改）。
- 适合**写多读少、并发冲突极高**的场景（如扣库存、转账）。
- 缺点：锁竞争严重，性能低，容易造成阻塞。

### 2. MySQL 实现方式

依赖 **InnoDB 行锁 / 表锁** + **事务**，分两种：

1. **共享锁（读锁）**：大家都能读，但没人能改
2. **排他锁（写锁）**：我独占，别人既不能读也不能改（最常用）

### 3. SQL 实战示例

#### 场景：商品扣库存（必须保证数据准确）

**前提**：表必须是 InnoDB 引擎，且查询条件有索引（才能行锁，否则表锁）

建表：

```sql
CREATE TABLE product (
    id INT PRIMARY KEY,
    stock INT NOT NULL COMMENT '库存',
    name VARCHAR(50)
) ENGINE=InnoDB;

INSERT INTO product VALUES (1, 100, '手机');
```

#### ① 加排他锁（FOR UPDATE）—— 最常用(悲观锁)

```sql
-- 开启事务
START TRANSACTION; 

-- 1. 查询并加排他锁：id=1 的数据被锁定，其他事务无法修改/加锁
SELECT * FROM product WHERE id = 1 FOR UPDATE; 

-- 2. 执行业务修改（扣库存）
UPDATE product SET stock = stock - 1 WHERE id = 1; 

-- 提交事务，锁自动释放
COMMIT; 
```

#### 关键规则

- 必须在**事务内**使用，锁在事务提交 / 回退后释放。
- `WHERE` 条件用**主键 / 唯一索引** → 行锁（只锁一行）；
- 无索引 → 表锁（锁全表，性能极差）。
- 其他事务尝试修改这条数据时，会**阻塞等待**，直到锁释放。

------

## 二、乐观锁（Optimistic Locking）

### 1. 核心理解

**很乐观，觉得没人抢数据**

- **不加锁**，正常查询修改。
- 修改时**校验数据版本**，如果被别人改了，就放弃本次修改，重试。
- 适合**读多写少、并发冲突低**的场景（如点赞、浏览量）。
- 缺点：冲突高时重试次数多，代码稍复杂。

### 2. MySQL 实现方式

最常用：**版本号机制**（给表加一个 version 字段）

1. 查询数据时，把版本号一起查出来
2. 修改时，`WHERE` 条件带上版本号
3. 版本号匹配才修改成功，同时版本号 + 1；不匹配就是冲突

### 3. SQL 实战示例

#### 场景：商品扣库存（乐观锁版）

先给表加版本字段：

```sql
ALTER TABLE product ADD COLUMN version INT DEFAULT 1 COMMENT '数据版本号';
```

#### 完整流程 SQL

```sql
-- 1. 【查询】先查出数据和当前版本号（不加锁）
SELECT id, stock, version FROM product WHERE id = 1; 
-- 假设结果：stock=100, version=1

-- 2. 【修改】关键！WHERE 必须带上 version，修改成功则 version+1
UPDATE product 
SET stock = stock - 1, version = version + 1 
WHERE id = 1 AND version = 1; 

-- 3. 判断结果：
-- 如果影响行数 = 1 → 修改成功
-- 如果影响行数 = 0 → 数据被别人改了，版本不匹配，冲突！
```

#### 冲突说明

- 如果两个请求同时查到 `version=1`
- 第一个修改成功，`version` 变成 2
- 第二个修改时 `version=1` 不满足 → 更新失败，需要重试

------

## 三、一句话总结对比

|    特性    |               悲观锁                |             乐观锁              |
| :--------: | :---------------------------------: | :-----------------------------: |
|  核心态度  |            别人一定会抢             |         别人大概率不抢          |
|  实现方式  |      数据库加锁（FOR UPDATE）       |       版本号 / 时间戳校验       |
|  加锁时机  |             操作前加锁              |       不加锁，修改时校验        |
|  并发性能  |           差（阻塞等待）            |     好（无锁，冲突才重试）      |
|  适用场景  | 写多读少、高并发冲突（库存 / 转账） | 读多写少、低冲突（点赞 / 统计） |
| 代码复杂度 |         低（SQL 加锁即可）          |       中（需要判断重试）        |

------

### 总结

1. **悲观锁**：`SELECT ... FOR UPDATE` + 事务，先锁再改，安全但性能低。
2. **乐观锁**：加 `version` 字段，修改时校验版本，无锁高性能，冲突需重试。
3. **选型口诀**：并发写多、数据敏感用悲观锁；读多写少、追求性能用乐观锁。

# Laravel 实战：悲观锁（FOR UPDATE）+ 乐观锁 完整代码示例

## 前提准备

1. 确保数据表是 **InnoDB 引擎**
2. 商品表 `products` 结构（和之前一致）：

```php
// 迁移文件
Schema::create('products', function (Blueprint $table) {
    $table->id();
    $table->integer('stock')->comment('库存');
    $table->string('name');
    $table->integer('version')->default(1)->comment('乐观锁版本号');
    $table->timestamps();
});
```

## 一、Laravel 悲观锁（FOR UPDATE）

**核心**：`lockForUpdate()` → 对应 SQL 的 `SELECT ... FOR UPDATE`

**场景**：扣库存、转账，必须强一致、高安全

### 完整代码

```php
use App\Models\Product;
use Illuminate\Support\Facades\DB;
use Illuminate\Database\QueryException;

// 扣库存逻辑（悲观锁）
function reduceStockPessimistic($productId, $num = 1)
{
    try {
        // 👇 开启事务 + 加悲观锁（关键）
        $product = DB::transaction(function () use ($productId, $num) {
            // lockForUpdate() = SELECT ... FOR UPDATE 加排他锁
            $product = Product::where('id', $productId)
                ->lockForUpdate() // 🔥 这行就是悲观锁
                ->firstOrFail();

            // 判断库存
            if ($product->stock < $num) {
                throw new \Exception("库存不足");
            }

            // 扣减库存
            $product->decrement('stock', $num);

            return $product;
        });

        return "扣减成功，剩余库存：{$product->stock}";

    } catch (QueryException $e) {
        return "锁等待超时/数据库异常";
    } catch (\Exception $e) {
        return $e->getMessage();
    }
}

// 调用
echo reduceStockPessimistic(1, 1);
```

## 关键点

1. **必须配合事务使用**，锁在事务提交 / 回滚后自动释放
2. `lockForUpdate()` 会锁住查询的那一行数据
3. 其他请求修改这条数据时，**会阻塞等待**，直到锁释放
4. 适合：库存、支付、订单等**高一致性**场景

## 二、Laravel 乐观锁（版本号机制）

**核心**：修改时校验 `version` 版本号，无锁高性能

**场景**：读多写少、冲突概率低（点赞、浏览量）

### 完整代码

```php
use App\Models\Product;

// 扣库存逻辑（乐观锁）
function reduceStockOptimistic($productId, $num = 1)
{
    // 最大重试次数（冲突时自动重试）
    $maxRetries = 3;
    $attempts = 0;

    while ($attempts < $maxRetries) {
        $attempts++;

        // 1. 查询数据和版本号（不加锁）
        $product = Product::findOrFail($productId);

        if ($product->stock < $num) {
            return "库存不足";
        }

        $currentVersion = $product->version;

        // 2. 🔥 关键：更新时带上版本号判断
        $affectedRows = Product::where('id', $productId)
            ->where('version', $currentVersion) // 版本匹配才允许更新
            ->update([
                'stock' => $product->stock - $num,
                'version' => $currentVersion + 1, // 版本号+1
            ]);

        // 3. 判断是否更新成功
        if ($affectedRows > 0) {
            return "扣减成功，剩余库存：" . ($product->stock - $num);
        }

        // 影响行数=0 → 版本冲突，重试
    }

    return "更新失败：并发冲突，请重试";
}

// 调用
echo reduceStockOptimistic(1, 1);
```

### 关键点

1. **全程不加锁**，性能极高
2. 更新时用 `where version = ?` 做冲突校验
3. 冲突时返回 `影响行数=0`，代码自动重试
4. 适合：读多写少、**高并发但冲突少**的场景

### 三、Laravel 中两者最核心区别

|    方式    |     代码关键字      |     锁机制     |       性能       |       适用场景       |
| :--------: | :-----------------: | :------------: | :--------------: | :------------------: |
| **悲观锁** |  `lockForUpdate()`  |  数据库排他锁  | 较低（阻塞等待） |  库存、支付、强一致  |
| **乐观锁** | `where(version, ?)` | 无锁，版本校验 |       极高       | 点赞、统计、读多写少 |

### 四、该记住的一句话

1. **Laravel 悲观锁** = 事务 + `lockForUpdate()`
2. **Laravel 乐观锁** = 版本字段 + 更新时校验版本
3. 金融 / 库存用**悲观锁**，普通业务用**乐观锁**

#   乐观锁使用场景

**乐观锁不是用来 “防并发” 的，是用来 “防覆盖” 的。**

你只要记住一句话：

**多人同时改同一条数据，但你不想让后提交的人覆盖掉先提交的人，就用乐观锁**

##  案例:后台编辑资料

假设有两个管理员：

A 和 B 同时打开了「商品详情编辑页」

1. A 先打开页面，看到：

   名称：iPhone

   价格：5000

2. B 紧接着也打开，看到的**也是同样的数据**

3. A 先改完提交：价格改成 5500

4. B 慢一点，改完提交：价格改成 5800

如果**没有乐观锁**：

B 会直接覆盖 A 的修改 → A 白改了。

如果**有乐观锁**：

B 提交时，系统发现版本不对，直接提示：

“数据已被他人修改，请刷新后重试”

B 就不会覆盖 A。

这就是乐观锁最典型、最常用的场景。

**你改之前，我先看看数据有没有被别人动过。**

动过 → 不让你改

没动 → 允许你改

典型业务：

- 后台商品编辑
- 用户资料修改
- 文章编辑
- 配置修改
- 订单备注修改

这些场景**并发冲突极少**，但一旦发生覆盖，后果很恶心。

什么时候**不要**用乐观锁？

这些场景必须用**悲观锁 / 分布式锁**，乐观锁扛不住：

1. **高并发秒杀、抢库存**
2. **钱包扣钱、转账、积分变动**
3. **高并发写入，冲突非常频繁**

因为乐观锁冲突了要重试，高并发下重试会炸。

用最简单的话总结

- **悲观锁**：谁先抢到谁先改，别人等着（适合抢钱、抢库存）
- **乐观锁**：谁先提交谁有效，后提交的不让覆盖（适合编辑、表单）

你可以粗暴理解：

**编辑页面用乐观锁，交易扣钱用悲观锁。**

Laravel 里最直观的乐观锁示例（表单编辑）

这就是**最真实、最常用**的乐观锁用法。

```php
// 1. 前端页面加载时，把版本号带上（隐藏域）
$product = Product::find($id);
return view('product.edit', compact('product'));

// 2. 更新时校验版本
public function update(Request $request, $id)
{
    $currentVersion = $request->version;

    $affected = Product::where('id', $id)
        ->where('version', $currentVersion)
        ->update([
            'name' => $request->name,
            'price' => $request->price,
            'version' => $currentVersion + 1,
        ]);

    if (!$affected) {
        return back()->withErrors('数据已被他人修改，请刷新重试');
    }

    return '修改成功';
}
```

# 补充

## 死锁情况下如何解决

### 什么是死锁 → 怎么产生的 → 怎么解决。

### 一、什么是死锁？

两个（或多个）事务

**互相持有对方需要的锁，又都在等对方释放锁**

谁也不让谁，就卡死了 → **死锁**

MySQL 检测到死锁后，会**主动杀掉其中一个事务**，让另一个执行。

所以你程序里会遇到报错：

> Deadlock found when trying to get lock; try restarting transaction

### 二、死锁是怎么产生的？（最经典场景）

## 场景：两个请求，按**相反顺序**锁数据

假设有两个商品：id=1、id=2

### 事务 A（请求 1）

1. 锁 id=1
2. 想去锁 id=2

### 事务 B（请求 2）

1. 锁 id=2
2. 想去锁 id=1

结果：

- A 拿着 1，等 2

- B 拿着 2，等 1

  

  → 

  互相等待 → 死锁

## 用 SQL 模拟死锁（你可以直接在 Navicat 开两个窗口试）

窗口 1（事务 A）

```sql
START TRANSACTION;
SELECT * FROM product WHERE id=1 FOR UPDATE; -- 锁住1
```

窗口 2（事务 B）

```sql
SELECT * FROM product WHERE id=2 FOR UPDATE; -- 等待B释放2
```

回到窗口 1：

```sql
SELECT * FROM product WHERE id=2 FOR UPDATE; -- 等待B释放2
```

回到窗口 2：

```sql
SELECT * FROM product WHERE id=1 FOR UPDATE; -- 等待A释放1
```

瞬间：**死锁产生**

### 三、在 Laravel 里怎么写出死锁？

非常常见：**循环锁多行，顺序乱了**

```php
// 事务A
DB::transaction(function(){
    Product::whereIn('id', [1,2])->lockForUpdate()->get();
});

// 事务B
DB::transaction(function(){
    Product::whereIn('id', [2,1])->lockForUpdate()->get();
});
```

顺序一反过来，高并发下必出死锁。

### 四、死锁产生的 4 个必要条件（背一下很有用）

只要破坏任意一个，死锁就不会发生

1. **互斥**：资源只能一个人用（锁特性，没法破）
2. **请求与保持**：拿着锁，又去申请别的锁
3. **不可剥夺**：锁不能被抢走，只能自己释放
4. **循环等待**：A 等 B，B 等 A，形成环路

**我们能破的只有：循环等待、请求与保持**

### 五、死锁怎么解决？（实战最有效方案）

#### 1. **固定加锁顺序（最有效、最通用）**

所有事务，锁多行时**必须按 ID 从小到大**

不要一会儿 [1,2] 一会儿 [2,1]

Laravel 写法：

```php
// 统一排序！
$ids = [2,1];
sort($ids); // [1,2]

Product::whereIn('id', $ids)->lockForUpdate()->get();
```

只要顺序统一，**90% 死锁直接消失**。

#### 2. **缩短事务时间**

事务越长，锁持有越久，越容易死锁

不要在事务里：

- 调用第三方 HTTP（微信 / 支付宝 / 快递接口）
- 写复杂逻辑、sleep、大计算
- 读大量无关数据

正确做法：

```php
// 错误：事务里干太多事
DB::transaction(function(){
    $http = file_get_contents('xxx'); // 慢
    $product->lockForUpdate()->first();
});

// 正确：事务只做数据库操作
$http = file_get_contents('xxx');
DB::transaction(function(){
    $product->lockForUpdate()->first();
});
```

#### 3. **避免同时锁多张表 / 多个资源**

比如：

- 锁 product
- 又锁 order
- 又锁 user

不同接口锁表顺序不一样 → 死锁

尽量**一次只锁最关键的一张表一行数据**。

#### 4. **开启死锁重试（Laravel 常用）**

MySQL 会自动杀一个事务，我们代码捕获异常并重试。

```php
use Illuminate\Database\QueryException;

function updateWithRetry(){
    $retry = 3;
    while($retry--){
        try {
            return DB::transaction(function(){
                // 锁逻辑
            });
        } catch (QueryException $e) {
            // 死锁异常
            if (str_contains($e->getMessage(), 'Deadlock')) {
                usleep(10000); // 等待10ms
                continue;
            }
            throw $e;
        }
    }
}
```

#### 5. **少用长事务、少用范围锁**

不要写：

```sql
SELECT * FROM order WHERE status=1 FOR UPDATE;
```

没有索引、范围过大 → 锁很多行 → 极易死锁

### 六、最简单总结（你记这个就够）

#### 死锁产生原因

**两个事务互相持有对方需要的锁，循环等待**

#### 死锁解决方法

1. **固定加锁顺序（按 ID 排序）** → 最有效
2. **缩短事务时间**
3. **避免同时锁多张表**
4. **死锁捕获重试**
5. **避免无索引的锁查询**

### 七、一句话口诀

**顺序统一、事务短小、锁行不锁表、死锁重试**

基本不会遇到死锁。

如果你需要，我可以给你写一段**能稳定复现死锁的 Laravel 压测代码**，让你直观看到报错。