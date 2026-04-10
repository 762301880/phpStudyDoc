## 一、基础优化（最简单，见效最快）

### 1. 配置文件优化（my.cnf/my.ini）

修改 MySQL 核心配置，不用改代码，性能直接提升：

```ini
[mysqld]
# 1. 连接数优化
max_connections = 1000          # 最大连接数
wait_timeout = 600             # 空闲连接超时关闭
interactive_timeout = 600

# 2. 内存优化（关键！）
innodb_buffer_pool_size = 70% 物理内存  # 核心：InnoDB 缓存池，越大越快
key_buffer_size = 256M         # MyISAM 索引缓存
query_cache_size = 0           # MySQL 8.0 已废弃，直接关闭

# 3. 日志优化
slow_query_log = 1             # 开启慢查询日志
slow_query_log_file = /var/log/mysql/slow.log
long_query_time = 1           # 超过1秒的SQL定义为慢查询
log_queries_not_using_indexes = 1  # 记录未使用索引的SQL

# 4. InnoDB 优化
innodb_flush_log_at_trx_commit = 2  # 平衡性能与安全（1=最安全，2=高性能）
innodb_log_file_size = 512M    # 日志文件大小
innodb_file_per_table = 1      # 每个表独立文件，方便维护
```

### 2. 存储引擎选择

- **99% 场景用 InnoDB**：支持事务、行锁、崩溃恢复，高并发必备
- 不要用 MyISAM：表锁、无事务、崩溃易丢数据

### 3. 系统层面优化

- 用 **SSD** 代替机械硬盘（MySQL 磁盘 IO 密集型，提升 10 倍以上）
- 关闭不必要的系统服务，释放内存
- Linux 系统调整文件描述符限制

## 二、SQL 与索引优化（80% 性能问题根源）

### 1. 索引优化（核心中的核心）

#### ✅ 必须建索引的场景

- `WHERE` 条件字段
- `JOIN` 关联字段
- `ORDER BY` / `GROUP BY` 排序字段

#### ❌ 不要建索引的场景

- 字段值重复率高（如性别、状态）
- 小表（数据量 < 1000 行）
- 频繁更新的字段（维护索引成本高）

#### 索引最佳实践

1. **最左前缀原则**：联合索引 `(a,b,c)`，查询必须从 a 开始匹配

2. 避免

   索引失效：

   - 不要在索引列上做运算、函数、类型转换
   - 避免 `!=` / `is null` / `like "%xxx"`

3. 单表索引数量控制在 **5 个以内**

### 2. SQL 语句优化

- 禁止 `SELECT *`，只查需要的字段
- 禁止 `select count(*)` 全表扫描，用缓存或统计表替代
- 分页优化：`limit 100000,10` 改成 `where id > 100000 limit 10`
- 避免子查询，用 `JOIN` 替代
- 大批量操作分批次执行（避免长事务）

### 3. 慢查询排查工具

```sql
-- 查看慢查询是否开启
show variables like '%slow_query_log%';

-- 查看执行计划（分析SQL是否用索引）
explain select * from user where name = '张三';
```

重点看 `type`（最好是 range/ref/eq_ref）、`key`（是否命中索引）、`rows`（扫描行数越少越好）

## 三、表结构设计优化

### 1. 字段类型优化

- 能用**数字**不用字符串（id、手机号用数字类型）
- 字符串用 `varchar` 代替 `char`，长度按需设置
- 日期用 `datetime` 代替字符串
- 大数据字段（文本、图片）不要存在主表，单独分表

### 2. 表设计规范

- 单表数据量**控制在 2000 万行以内**，超过分表
- 禁止使用外键约束（用业务代码保证一致性，提升性能）
- 给所有表加 `unsigned int` 自增主键
- 字段尽量设置 `NOT NULL`，用默认值代替 NULL

------

## 四、架构级优化（高并发、大数据场景）

### 1. 读写分离

- 主库写，从库读，分摊查询压力
- 用中间件：MyCat、ShardingSphere

### 2. 分库分表

- 垂直分表：把大表拆成小表（冷热数据分离）
- 水平分表：按 id / 时间拆分（如订单表按月份拆分）

### 3. 缓存优化

- 加 Redis 缓存热点数据，减少 MySQL 查询
- 缓存击穿 / 穿透 / 雪崩问题提前处理

### 4. 其他优化

- 定期优化表：`optimize table 表名`
- 定期清理历史数据
- 数据库定时备份，避免故障

------

# 快速优化 Checklist（直接照着做）

1. 开启慢查询日志，定位最慢的 SQL
2. 给高频查询 SQL 加合适索引
3. 调整 innodb_buffer_pool_size 为物理内存 70%
4. 禁止 select * 和无效索引
5. 单表数据量超 2000 万立即分表
6. 高并发场景做读写分离 + 缓存

------

### 总结

1. **80% 性能问题**来自慢 SQL 和缺失索引，优先解决这两个
2. 配置优化是基础，内存分配直接决定 MySQL 速度
3. 大数据高并发必须靠架构优化（分库分表、读写分离）
4. 优化是持续过程，定期监控慢查询和服务器状态

# mysql解决幻读问题

在 Laravel 中解决 MySQL 幻读，核心是利用 **InnoDB 的可重复读（RR）隔离级别 + 临键锁（Next-Key Lock）**，配合 Laravel 提供的 **悲观锁（lockForUpdate）** 与事务机制，从数据库层面彻底阻断并发插入。

### 一、幻读是什么

**幻读**：同一事务内，两次相同范围查询，后一次出现前一次没有的**新插入行**。

- 事务 A：`SELECT COUNT(*) FROM orders WHERE status=1;` → 结果 10 条
- 事务 B：插入 1 条 `status=1` 的订单并提交
- 事务 A：再次查询 → 结果 11 条（出现幻行）

### 二、Laravel 解决方案（3 种）

#### 1. 首选：使用 `lockForUpdate()`（当前读 + 临键锁）

**原理**：

`SELECT ... FOR UPDATE` 会触发 **Next-Key Lock（记录锁 + 间隙锁）**，锁定满足条件的**所有行 + 行之间的间隙**，其他事务无法在该区间插入，彻底防幻读。

**Laravel 写法（推荐）**：

php



运行









```
use Illuminate\Support\Facades\DB;

DB::beginTransaction();
try {
    // 🔒 范围查询 + 悲观锁（关键）
    $count = DB::table('orders')
        ->where('status', 1)
        ->lockForUpdate() // 生成 SELECT ... FOR UPDATE
        ->count();

    // 业务逻辑（插入/更新）
    DB::table('orders')->insert([
        'status' => 1,
        'amount' => 100,
    ]);

    DB::commit();
} catch (\Exception $e) {
    DB::rollBack();
    throw $e;
}
```

**Eloquent 写法**：

php



运行









```
use App\Models\Order;

DB::transaction(function () {
    // 锁整个范围
    $orders = Order::where('status', 1)->lockForUpdate()->get();
    // 插入新订单
    Order::create(['status' => 1, 'amount' => 100]);
});
```

**要点**：

- **必须走索引**：`WHERE status=1` 字段要有索引，否则锁全表
- **范围条件**：`> / < / between / like` 才会加间隙锁；`WHERE id=1` 只加记录锁（不防幻读）

#### 2. 全局 / 会话提升隔离级别（Serializable）

**原理**：

最高级别 **SERIALIZABLE**，所有 `SELECT` 隐式加锁，事务完全串行，**绝对无幻读**，但**并发性能极差**。

**Laravel 配置（config/database.php）**：

php



运行









```
'mysql' => [
    // ...
    'isolation_level' => 'SERIALIZABLE', // 全局设置
],
```

**事务内临时设置**：

php



运行









```
DB::transaction(function () {
    // 仅当前事务生效
    DB::statement("SET TRANSACTION ISOLATION LEVEL SERIALIZABLE");
    
    $count = DB::table('orders')->where('status', 1)->count();
    DB::table('orders')->insert(['status' => 1]);
}, 5); // 死锁自动重试 5 次
```

**适用**：

- 金融、库存等**强一致性**场景
- 并发极低、不追求性能

#### 3. 替代方案：唯一约束 + INSERT ... ON DUPLICATE KEY UPDATE

**原理**：

把 “先查后插” 改为**原子插入**，用**唯一索引**从源头阻止重复插入，**无幻读风险**。

**示例（防重复创建）**：

sql











```
-- 给 (user_id, type) 加唯一索引
ALTER TABLE orders ADD UNIQUE idx_user_type (user_id, type);
```

php



运行









```
// 原子插入，不存在则插入，存在则更新
DB::table('orders')->updateOrInsert(
    ['user_id' => 100, 'type' => 'monthly'], // 唯一条件
    ['amount' => 200, 'status' => 1]         // 插入/更新数据
);
```

**优点**：

- 无锁、性能最好
- 彻底避免幻读与并发冲突

### 三、关键机制与注意事项

1. **Next-Key Lock 生效条件**

   

   - 隔离级别：**REPEATABLE READ（MySQL 默认）**
   - 查询：**当前读**（`FOR UPDATE / LOCK IN SHARE MODE`）
   - 条件：**范围查询 + 索引**（无索引锁全表）

   

2. **MVCC 只能防快照读**

   

   - 普通 `SELECT`（快照读）：靠 MVCC 读历史快照，**看不到新插入行**
   - `SELECT ... FOR UPDATE`（当前读）：必须靠 **Next-Key Lock** 才防幻读

   

3. **Laravel 事务最佳实践**

   

   php

   

   运行

   

   

   

   

   ```
   DB::transaction(function () {
       // 1. 先锁（越早越好）
       $data = DB::table('table')->where('x', 1)->lockForUpdate()->get();
       
       // 2. 仅数据库操作（禁 HTTP/文件/循环等耗时逻辑）
       DB::table('table')->insert(...);
       
       // 3. 快速提交（锁时间越短越好）
   }, 5); // 死锁重试
   ```

   

### 四、方案对比

表格







| 方案                     | 性能  | 防幻读 | 适用场景                      |
| ------------------------ | ----- | ------ | ----------------------------- |
| **lockForUpdate() + RR** | ⭐⭐⭐⭐  | ✅ 完全 | 高并发、范围查询、库存 / 订单 |
| **SERIALIZABLE**         | ⭐     | ✅ 完全 | 强一致、低并发、金融          |
| **唯一索引 + 原子插入**  | ⭐⭐⭐⭐⭐ | ✅ 完全 | 防重复、先查后插场景          |

### 五、总结

- **90% 场景**：用 **`lockForUpdate()` + 可重复读（RR）**，性能与一致性平衡最佳
- **强一致**：用 **SERIALIZABLE**（牺牲并发）
- **防重复插入**：优先 **唯一索引 + `updateOrInsert`**（无锁最优）

#   mysql索引为什么快

## 复杂度O(N) 名词解析

O → **Order**

读音：/ˈɔːdə(r)/

意思：**阶、次序、量级**

O (n) 里的 O 就是 Order 的缩写，表示**复杂度的级别**。

N → **Number**

读音：/ˈnʌmbə(r)/

意思：**数量、个数**

就是数据有多少条、元素有多少个。

合起来直译

**O(n)**

= Order of Number

= **跟数量成正比的费劲程度**

大白话翻译：

**数据有多少个，就要找多少次。**

合在一起，O (n) 是什么意思？

**O (n) = 东西有多少，你就要找多少次**

举个最简单的例子：

你在**一堆乱牌里找一张 A**

牌有多少张，你就得翻多少次。

- 10 张牌 → 翻 10 次
- 100 张牌 → 翻 100 次
- 10000 张牌 → 翻 10000 次

东西越多，你越累，越慢。

这就叫 **O(n)**。

##  O (log n) 

- ### 英文

  **O(log n)**

  = **Order of the logarithm of n**

  ### 单词拆开

  - **O** = **Order**（量级、程度）
  - **log** = **logarithm**（对数）
  - **n** = **number**（数量）

  ------

  ### 人话意思

  **数量变多，但找的次数几乎不变，只增加一点点。**

  举个最简单例子：

  - 10 个东西 → 找 3 次
  - 100 个东西 → 找 7 次
  - 1000 个东西 → 找 10 次

  数量翻 10 倍，查找次数只多一点点。

### log /logarithm 到底是什么？

logarithm = 指数反过来算

### 用最通俗的例子：猜数字

我心里想一个 1～100 的数字，让你猜。

你怎么猜最快？

不是从 1、2、3… 挨个猜（那是 O (n)）

而是**每次砍一半**：

- 50？大了 → 范围变成 1～49
- 25？小了 → 范围变成 26～49
- 37？大了 → 范围继续缩

你会发现：

**最多猜 7 次，一定能猜到。**

这种**每次砍一半**的查找方式，

数学上就叫 **log(n)**。

## MySQL 索引为什么快？通俗 + 原理全解析

一句话结论

**MySQL 索引快，本质是：把「无序的全表遍历」变成「有序的二分查找」，从 O (n) 降到 O (log n)，查询次数指数级减少。**

### 1. 没有索引：MySQL 怎么查？

假设你有一张 1000 万行的用户表，执行：

```sql
SELECT * FROM user WHERE name = '张三';
```

**无索引时**：

- MySQL 必须**一行一行从头扫到尾**（全表扫描）
- 最坏情况要查 **1000 万次**
- 时间复杂度：**O(n)**

就像**一本没有目录的书**，找一个词必须逐页翻。

### 2. 有索引：MySQL 怎么查？

索引就是**给数据库建的目录**。

**有索引时**：

- 索引是**排好序的结构**
- MySQL 不用全表扫描
- 直接用**二分查找**快速定位
- 1000 万数据，**最多查 20 多次**就能找到
- 时间复杂度：**O(log n)**

### 3. 核心原因：索引底层是 B+ 树

MySQL 默认索引结构是 **B+ 树**，它快的 4 个关键点：

#### ① 数据**有序**

- 索引列自动排序
- 不用遍历，直接**范围查找 / 二分查找**

#### ② **层级少，查询次数极少**

- 1000 万数据 → B+ 树高度通常只有 **3~4 层**
- 意味着：**只需要 3~4 次磁盘 I/O 就能找到数据**
- 无索引可能需要**上万次 I/O**

#### ③ **减少磁盘 I/O（最关键！）**

数据库慢 99% 不是 CPU 慢，而是**磁盘 I/O 慢**。

- 无索引：大量随机 I/O
- 有索引：极少 I/O，且是顺序 I/O

#### ④ **叶子节点是链表，范围查询超快**

```
WHERE id > 100 AND id < 200
```

- 直接在有序链表上遍历，不用回表、不用扫描全表

### 4. 用数字感受速度差距

数据量：**1 亿条**

|      查询方式      | 查找次数 |        速度         |
| :----------------: | :------: | :-----------------: |
| 无索引（全表扫描） |  1 亿次  | 极慢（秒 / 分钟级） |
|  有索引（B + 树）  | 约 25 次 |   极快（毫秒级）    |

**速度差：几十万倍！**

### 5. 为什么不用哈希、二叉树？

你可能会问：哈希不是 O (1) 吗？为什么不用？

#### 哈希索引

- 优点：精确查询 = 极快
- 缺点：**不支持范围查询**（> < between），用不了排序

#### 普通二叉树

- 缺点：数据有序时会变成**链表**，高度极高，I/O 爆炸

#### B+ 树

- 矮、胖、稳定
- 支持**范围查询 + 排序 + 分组**
- 最适合数据库场景

### 6. 简单总结（最核心）

MySQL 索引快，就 3 点：

1. **有序** → 不用全表扫描
2. **B+ 树结构** → 层数极少，磁盘 I/O 极少
3. **从 O (n) → O (log n)** → 查询次数指数级下降

------

### 总结

- **无索引 = 逐行翻书**
- **有索引 = 看目录定位**
- **B+ 树 = 让目录超级高效、超级矮胖**

#  分布式事务

##  mq处理分布式

Laravel + MQ（比如 RabbitMQ）实现分布式事务/最终一致性

### 结论（你最关心的点）

👉 **Laravel 里用 MQ 做分布式事务，本质不是“强一致”，而是：最终一致性**

核心思路就一句话：

> **本地事务成功 → 发MQ消息 → 其他服务消费并完成自己的事务**

### 典型业务：订单 + 库存

你刚才问的其实就是这个场景：

- 下单（订单服务）
- 扣库存（库存服务）

### 分布式

MQ 分布式事务标准玩法

核心流程

1️⃣ 订单服务（生产者）

```php
DB::transaction(function () use ($data) {
    // 1. 创建订单（状态：待扣库存）
    $order = Order::create([
        'status' => 'pending'
    ]);

    // 2. 记录消息（本地消息表）
    DB::table('mq_messages')->insert([
        'order_id' => $order->id,
        'status' => 'pending'
    ]);
});
```

注意：**订单 + 消息记录 必须在同一个事务里**

**发送 MQ（异步）**

```php
// 定时任务 or 事务后触发
$messages = DB::table('mq_messages')
    ->where('status', 'pending')
    ->get();

foreach ($messages as $msg) {
    // 发MQ
    $mq->publish('stock.deduct', $msg);

    // 标记已发送
    DB::table('mq_messages')
        ->where('id', $msg->id)
        ->update(['status' => 'sent']);
}
```

这一步解决：**消息不丢**

**库存服务（消费者）**

```php
public function handle($msg)
{
    DB::transaction(function () use ($msg) {

        // 幂等处理（防止重复消费）
        $exists = DB::table('mq_log')
            ->where('msg_id', $msg->id)
            ->exists();

        if ($exists) return;

        $stock = Stock::where('product_id', $msg->product_id)
            ->lockForUpdate()
            ->first();

        if ($stock->num <= 0) {
            throw new \Exception('库存不足');
        }

        $stock->decrement('num');

        // 记录已消费
        DB::table('mq_log')->insert([
            'msg_id' => $msg->id
        ]);
    });
}
```

**回调 or 状态更新**

库存成功后，可以：

- 发一个 MQ 回去
- 或订单服务查询状态

更新订单：

```php
Order::where('id', $msg->order_id)->update(['status' => 'success']);
```

