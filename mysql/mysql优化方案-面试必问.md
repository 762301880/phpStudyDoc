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