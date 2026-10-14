## 什么情况下需要考虑分库分表

###  单表数据量过大（最核心场景）

当单张表的数据量达到一定规模时，会导致：

- **查询变慢**：索引失效或查询扫描行数过多（例如 MySQL 单表超过 1000 万行后，查询性能明显下降）。
- **写入 / 更新卡顿**：频繁的插入 / 更新会导致表锁 / 行锁竞争加剧，事务提交变慢。
- **索引维护成本高**：索引文件过大，插入数据时索引更新耗时增加。



**经验值**：

- MySQL：单表行数建议控制在 500 万～1000 万以内（视字段多少和查询复杂度调整）。
- 其他数据库（如 PostgreSQL、SQL Server）阈值略高，但超过 1 亿行通常需要分片。

### 数据库读写压力过大

- **读压力大**：当单库的 QPS（每秒查询量）过高（例如超过 1 万 QPS），数据库连接池频繁满员，查询响应延迟。
- **写压力大**：高频写入（如日志、订单系统）导致磁盘 IO 瓶颈，单库写入吞吐量不足。



分库可以将读写压力分散到多个数据库实例，避免单库过载。

### 业务增长可预见，提前规避瓶颈

如果业务处于快速增长期（如用户量、订单量按月翻倍），即使当前数据量不大，也需要提前规划分库分表：

- 避免后期数据量爆炸时，紧急拆分导致的业务中断。
- 例如：社交平台的用户表、电商的订单表，这类数据通常持续增长且难以清理。

###  数据热点问题突出

某些表存在「热点数据」，即部分数据被高频访问，导致：

- 热点行竞争：例如某热门商品的库存表，大量并发更新导致锁等待。
- 分区倾斜：数据分布不均（如某地区用户数据占比 80%），单表 / 单库负载过高。

分库分表可将热点数据分散到不同分片，均衡负载。

### 存储容量限制

当单库数据文件过大（例如超过 100GB），会导致：

- 备份 / 恢复耗时过长（可能需要数小时）。
- 磁盘空间不足，且单服务器扩容成本高（相比多服务器分布式存储）。

分库可将数据分散到多台服务器的磁盘，突破单节点存储限制。

### 不需要分库分表的场景

- 数据量小（如几万行）且增长缓慢（如配置表、字典表）。
- 业务访问频率低（如历史归档数据，每月仅查询几次）。
- 初期快速迭代阶段，优先保证业务上线，待数据规模明确后再考虑分片。

**总结**：分库分表是「治大病」的方案，会增加系统复杂度（如跨分片查询、事务处理）。建议在数据量 / 访问量接近数据库性能阈值时，结合业务增长趋势，提前规划实施。

在 ThinkPHP 中实现分库分表，主要是为了解决数据量过大导致的性能问题。分库是将数据拆分到不同的数据库，分表是将一张大表拆分成多个小表。对于新手来说，可以从基础的手动分片开始理解，再逐步使用框架扩展或中间件。

---

## 分库实战

- **分库**：按规则将数据分散到多个数据库（如按用户 ID 范围分到 db1、db2）。

### 水平分库(推荐)

MySQL 分库（水平分库，和 `user_id%4` 分表逻辑对应）

> 分表：同一个库里面，拆成多张表 `user_0` ~ `user_3` **分库：多个独立数据库实例 / 库，数据分散到不同库**，路由规则同样可以用 `user_id % N`

最简单：按 user_id 取模分库（和分表思路一致）

假设拆成 **4 个库**： `db_user_0`、`db_user_1`、`db_user_2`、`db_user_3`

路由公式：

```bash
db_index = user_id % 4
目标库 = db_user_{db_index}
```

- user_id=100 → 100%4=0 → 落到 `db_user_0`
- user_id=101 → 101%4=1 → 落到 `db_user_1`

每个库内部**还可以继续分表**（库 + 表两级分片，最常用）：

```php
db_index = user_id % 4
table_index = user_id % 8
库名：db_user_{db_index}
表名：user_{table_index}
```

> 4 库 × 8 表 = 32 张物理表。ShardingSphere 就是这种库 + 表二级分片。

### 范围分库（id 区间）

示例：

- db_user_0：user_id 1 ~ 1000w
- db_user_1：user_id 1000w ~ 2000w
- db_user_2：user_id 2000w ~ 3000w

优点：扩容简单，新增库只需要新开一段区间，**老数据不用动** 缺点：容易出现热点，新数据全部写入最后一个库。





---

## 分表实战

### 手动id代码示例

- **分表**：将一张表拆分成多个子表（如 user 表拆成 user_0、user_1、...、user_n）。
- **分片策略**：常见的有「范围分片」（如 ID 1-10000 放表 0）、「哈希分片」（如 ID 取模分表）等。

#### id分配数据表

原理：单独建一张 `id_generator` 表，**全局唯一 ID 的源头在 MySQL**，Redis 只做号段缓存提升性能。 每次去 DB 拿一段连续 ID（比如一次拿 1000 个）放到 Redis，业务从 Redis 消费；号段耗尽再去 DB 取下一段。 优点：

- ID 源头在 MySQL，持久可靠，Redis 挂了不重复
- DB 压力极低，1000 次 ID 请求才一次 DB
- ID 是数字自增，和原生主键风格一致，适合分表
- 分表扩容、新增分表完全不用改 ID 生成逻辑

```sql
-- 创建ID生成器表
CREATE TABLE `id_generator` (
  `id` INT UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键',
  `biz_type` VARCHAR(64) NOT NULL COMMENT '业务类型：users / orders',
  `current_max_id` BIGINT UNSIGNED NOT NULL DEFAULT 0 COMMENT '当前已分配最大ID',
  `step` INT UNSIGNED NOT NULL DEFAULT 1000 COMMENT '每次预取号段长度',
  `created_at` DATETIME NULL DEFAULT NULL,
  `updated_at` DATETIME NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `uk_biz_type` (`biz_type`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='全局ID号段生成表';

-- 初始化 users 业务记录
INSERT INTO `id_generator` (`biz_type`, `current_max_id`, `step`, `created_at`, `updated_at`)
VALUES ('users', 0, 1000, NOW(), NOW());
```

**对应laravel数据迁移**

迁移文件 `create_id_generator_table.php`

```php
<?php

use Illuminate\Database\Migrations\Migration;
use Illuminate\Database\Schema\Blueprint;
use Illuminate\Support\Facades\Schema;

return new class extends Migration
{
    public function up(): void
    {
        Schema::create('id_generator', function (Blueprint $table) {
            $table->increments('id');
            // 业务类型：users / orders
            $table->string('biz_type', 64)->unique();
            // 当前已经分配出去的最大ID
            $table->unsignedBigInteger('current_max_id')->default(0);
            // 号段长度，每次预取多少个ID
            $table->unsignedInteger('step')->default(1000);
            $table->timestamps();
        });

        // 初始化用户业务记录
        \DB::table('id_generator')->insert([
            'biz_type' => 'users',
            'current_max_id' => 0,
            'step' => 1000,
        ]);
    }

    public function down(): void
    {
        Schema::dropIfExists('id_generator');
    }
};
```

#### ID 生成服务类 

`app/Services/IdGeneratorService.php`

```php
<?php

namespace App\Services;

use Illuminate\Support\Facades\DB;
use Illuminate\Support\Facades\Redis;

class IdGeneratorService
{
    protected string $bizType;
    protected string $redisKeyPrefix = 'id_generator:';
    // 本地内存缓存当前号段，减少Redis请求（可选优化）
    protected static array $localSegment = [];

    public function __construct(string $bizType = 'users')
    {
        $this->bizType = $bizType;
    }

    /**
     * 获取下一个全局唯一ID
     */
    public function nextId(): int
    {
        $redisKey = $this->redisKeyPrefix . $this->bizType;

        // 1. 先从Redis拿可用ID
        $id = Redis::lpop($redisKey);
        if ($id !== false && $id !== null) {
            return (int)$id;
        }

        // 2. Redis号段耗尽，去DB申请新号段，加分布式锁防止并发多次申请号段
        $lockKey = $redisKey . ':lock';
        //$lock = Redis::set($lockKey, 1, ['nx', 'ex' => 3]);
        // ✅ PhpRedis原生写法：set(key, val, 过期标识, 过期秒数, 标记NX)
        $lock = Redis::set($lockKey, 1, 'ex', 3, 'nx');
        if (!$lock) {
            // 抢锁失败，短暂重试
            usleep(50);
            return $this->nextId();
        }

        try {
            // 3. DB事务更新号段：原子更新current_max_id
            $segment = DB::transaction(function () {
                $row = DB::table('id_generator')
                    ->where('biz_type', $this->bizType)
                    ->lockForUpdate()
                    ->first();

                if (!$row) {
                    throw new \RuntimeException("biz_type {$this->bizType} not found in id_generator");
                }

                $oldMaxId = $row->current_max_id;
                $newMaxId = $oldMaxId + $row->step;

                DB::table('id_generator')
                    ->where('biz_type', $this->bizType)
                    ->update([
                        'current_max_id' => $newMaxId,
                        'updated_at' => now()
                    ]);

                // 返回本次拿到的号段区间：[oldMaxId +1 ~ newMaxId]
                return [
                    'start' => $oldMaxId + 1,
                    'end' => $newMaxId
                ];
            });

            // 4. 把整个号段批量压入Redis list
            $ids = [];
            for ($i = $segment['start']; $i <= $segment['end']; $i++) {
                $ids[] = $i;
            }
            Redis::rpush($redisKey, ...$ids);
            // 设置过期时间（兜底，防止脏数据永久残留，比如服务重启剩余号段丢掉也没关系，最多浪费少量ID）
            Redis::expire($redisKey, 86400 * 7);

            // 弹出第一个ID返回
            return (int)Redis::lpop($redisKey);
        } finally {
            Redis::del($lockKey);
        }
    }
}
```

#### 用户分表插入示例（Laravel）

```php
<?php

namespace App\Services\UserService;

use App\Services\IdGeneratorService;
use Illuminate\Support\Facades\DB;

class UserService
{
    public function createUser(array $data)
    {
        $idGen = new IdGeneratorService('users');
        $userId = $idGen->nextId();

        // 分表路由：对全局ID取模，决定落到哪一张分表
        $tableIndex = $userId % 4;
        $tableName = "users_{$tableIndex}";

        return DB::table($tableName)->insert([
            'id' => $userId,
            'name' => $data['name'],
            'phone' => $data['phone'],
            'created_at' => now(),
            'updated_at' => now(),
        ]);
    }
}
```

关键点说明：

1. **ID 是全局唯一，然后根据 ID 取模路由到分表**，不是分表各自自增 id
2. `lockForUpdate()` 悲观锁保证 DB 号段原子更新，不会并发拿到重复号段
3. Redis 只是缓存号段，**数据源头是 MySQL**，Redis 清空最多浪费未使用 ID，不会 ID 重复
4. 分表扩容：比如从 4 张扩到 8 张，只需要改路由 `$userId % 8`，**ID 生成器代码完全不动**

###  ID Snowflake（高并发场景，大厂常用）

> 适合：QPS 极高，不想频繁操作 MySQL/Redis，ID 自带时间戳，无序自增。 缺点：ID 是 64 位大整数，不能保证严格连续，会有 ID 空洞；数据库字段必须 `bigint`。

Laravel 可以直接用现成包：`godruoyi/php-snowflake`

```php
composer require godruoyi/php-snowflake
```

```php
<?php
namespace App\Services;
use Godruoyi\Snowflake\Snowflake;

class SnowflakeIdService
{
    protected Snowflake $snowflake;

    public function __construct()
    {
        $this->snowflake = new Snowflake();
        // 机器ID，多实例部署必须保证每台机器唯一 0~31
        $this->snowflake->setMachineId(1);
    }

    public function nextId(): string
    {
        return $this->snowflake->id();
    }
}
```

用户插入分表示例：

```php
public function createUser(array $data)
{
    $snow = new SnowflakeIdService();
    $userId = $snow->nextId();
    $tableIndex = $userId % 4;
    $tableName = "users_{$tableIndex}";
    DB::table($tableName)->insert([
        'id' => $userId,
        'name' => $data['name'],
    ]);
}
```

 雪花 ID 坑：

- 机器 ID 必须全局唯一，多服务实例不能重复

- 依赖服务器时间，**时间回拨会产生重复 ID**（生产必须处理时间回拨）

- ID 不连续，审计排查不如号段直观

  

### ThinkPHP 分片扩展

- **tp5-sharding**（适用于 TP5）：通过配置文件定义分片规则，自动处理分库分表。

- 安装：`composer require lizhichao/think-sharding`

- 配置：在

  ```
  config/sharding.php
  ```

  中定义分片规则，例如：

  ```php
  return [
      'user' => [ // 表名
          'type' => 'hash', // 哈希分片
          'num' => 4, // 4张分表
          'field' => 'id', // 分片字段（按id哈希）
      ],
  ];
  ```

- 使用：模型中继承分片模型，自动按规则分表。

### 数据库中间件（适合大规模场景）

如果数据量极大，可使用中间件（如 MyCat、Sharding-JDBC），应用层（ThinkPHP）无需修改代码，中间件自动处理分库分表。

### 四、注意事项

1. **分片策略设计**：尽量选择查询频繁的字段作为分片键（如用户 ID），避免跨表 / 跨库查询。
2. **事务问题**：跨库事务难以保证，设计时尽量避免。
3. **扩容问题**：提前规划分片数量（如按 2 的 n 次方分表，方便后期扩容）。
4. **索引一致性**：所有分表的表结构和索引必须一致。

对于新手，建议先从「按 ID 取模分表」这种简单场景练手，理解分片逻辑后，再根据业务复杂度选择扩展或中间件。

分库分表是应对数据库性能瓶颈的重要手段，但并非所有场景都需要。通常在以下情况，需要考虑引入分库分表：



