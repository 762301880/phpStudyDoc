mysql查询优化

> **参考**
>
> https://blog.csdn.net/congge_study/article/details/127712927
>
> https://mp.weixin.qq.com/s/MArWD5iDi6Sv0r0vfebt5A
>
> https://mp.weixin.qq.com/s/vGSIzk_YeF6dn1nHyrYv7A
>
> https://www.jianshu.com/p/3ab117c83d0b
>
> https://zhuanlan.zhihu.com/p/658592367

## 一,联合索引

### 什么是联合索引

- **联合索引（Composite Index）**：在数据库表中对**多个列组合**建立的索引。
- 本质上它是一个按照 `(col1, col2, col3, …)` 排序的 B+ 树。
- 查询能否利用联合索引，取决于 **最左前缀原则**。

### 案例讲解

假设有一张订单表 `orders`：

```mysql
CREATE TABLE orders (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    order_date DATETIME NOT NULL,
    status TINYINT NOT NULL,
    INDEX idx_user_date_status (user_id, order_date, status)
);
```

这里我们创建了一个**联合索引**：

```mysql
(user_id, order_date, status)
```

它在底层是按照 **user_id → order_date → status** 的顺序排序的。

#### 案例 1：完全匹配

```mysql
SELECT * 
FROM orders 
WHERE user_id = 1001 AND order_date = '2025-09-15' AND status = 1;
```

✅ 能完整利用 `(user_id, order_date, status)`，三列条件都命中。

#### 案例 2：部分匹配（前缀原则）

```mysql
SELECT * 
FROM orders 
WHERE user_id = 1001 AND order_date = '2025-09-15';
```

✅ 能用 `(user_id, order_date)` 部分索引，仍然高效。

#### 案例 3：跳过中间列(不会走联合索引)

```mysql
SELECT * 
FROM orders 
WHERE user_id = 1001 AND status = 1;
```

⚠️ **不能直接利用 `(user_id, order_date, status)`**，因为跳过了 `order_date`。
 索引顺序是固定的，必须连续从最左开始。

#### 案例 4：范围查询阻断

```mysql
SELECT * 
FROM orders 
WHERE user_id = 1001 AND order_date >= '2025-09-01' AND status = 1;
```

👉 这里 `user_id` 命中，`order_date` 用到了范围查询（>=），但是 **范围条件会阻断后续列**。
 结果：只能用 `(user_id, order_date)`，**status 不再走索引**。

####  案例5： 不按顺序执行(也可以走索引)

```sql
EXPLAIN SELECT * 
FROM orders 
WHERE status = 1 and user_id = 1001 AND order_date = '2025-09-15' ;
```

**因为 MySQL 有个「查询优化器」，它会自动把你乱序的 WHERE 条件，重新排成索引需要的顺序 → 所以照样走索引！**

不是你写什么顺序，MySQL 就按什么顺序查。

 一句话核心原理

**联合索引要求：字段必须包含 a → 再包含 b → 再包含 c但不要求：你写 SQL 的顺序必须是 a,b,c**

MySQL 优化器会**自动整理条件顺序**，和你写的顺序无关。



###   常见适用场景

1. **多条件过滤查询**
   - 例如电商订单：经常按 `(user_id, order_date)` 查最近订单。
2. **排序优化**
   - 如果 SQL 有 `ORDER BY user_id, order_date`，联合索引可以避免额外排序。
3. **覆盖索引**
   - 如果查询只涉及索引里的列，就能避免回表查询。

### 设计原则

- **最左前缀匹配**：索引列的顺序要结合查询的 `WHERE` 和 `ORDER BY` 习惯。
- **等值放前，范围放后**：比如 `(user_id, status, order_date)` 会比 `(user_id, order_date, status)` 更好，如果 order_date 经常是范围查询。
- **避免冗余索引**：有 `(a, b)` 联合索引时，单独的 `(a)` 索引通常就不需要了。

### 总结口诀

👉 **左列必须用，跳列就报废；范围一出现，右边全作废。**

### 索引会失效场景

联合索引 (a,b,c) 失效的 10 种情况（背会就无敌）

```sql
CREATE TABLE `test` (
  `id` int NOT NULL AUTO_INCREMENT,
  `a` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '字段a',
  `b` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci NOT NULL COMMENT '字段b',
  `c` varchar(255) NOT NULL,
  `d` varchar(255) NOT NULL,
  PRIMARY KEY (`id`),
  KEY `联合索引` (`a`,`b`,`c`) USING BTREE COMMENT '联合索引abc'
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;
```



1. **不按最左前缀，跳过最左字段（最常见）**

索引是 **a → b → c**

你必须**从 a 开始用**，跳过 a 直接查 b、c → **索引直接失效**

## 2. **中间断了（a 有，但跳过 b）**

```sql
WHERE a='1' AND c='3'  -- 失效！跳过 b
```

只能用到 **a**，b、c 用不上索引。

------

## 3. **使用 OR 连接条件（致命）**

```sql
WHERE a='1' AND b='2' OR c='3'  -- 失效！
```

**OR 会直接破坏最左前缀**，全表扫描。

------

## 4. **字段类型不匹配（隐式转换）**

a 是 varchar，你用数字查：

```sql
WHERE a=1  -- 失效！字符串 = 数字
```

必须写成：

```sql
WHERE a='1'
```

------

## 5. **使用！=、NOT IN、NOT EXISTS、IS NOT NULL**

```sql
WHERE a!='1'
WHERE a NOT IN ('1','2')
```

**范围否定会导致索引失效**。

------

## 6. **LIKE 以 % 开头（左模糊）**

```sql
WHERE a LIKE '%123'  -- 失效
WHERE a LIKE '123%'  -- 有效！
```

**% 在左边 → 索引失效**

------

## 7. **对索引字段使用函数、运算**

```sql
WHERE SUBSTRING(a,1,1)='1'
WHERE UPPER(a)='A'
WHERE a + 1 = 2
```

**只要字段被计算 → 索引失效**

------

## 8. **ORDER BY / GROUP BY 违反最左前缀**

```sql
ORDER BY b,c     -- 没有 a → 失效
ORDER BY a,c     -- 跳过 b → 失效
```

------

## 9. **MySQL 优化器判断：全表更快**

表数据**很少**（比如只有 5 行）

MySQL 觉得：**全表扫描比走索引更快**

→ 主动放弃索引，走全表。

**这不是失效，是优化器选择。**

------

## 10. **联合索引字段出现 NULL 判断**

```sql
WHERE a IS NULL
```

如果字段允许 NULL，**IS NULL 可能不走索引**。

最核心总结（背这 4 句就够）

1. **必须从最左字段 a 开始用**
2. **不能跳过中间字段**
3. **OR、!=、% xxx、函数 → 必失效**
4. **类型必须匹配，字符串别用数字**

可以直接用的万能判断口诀

**最左不能丢，中间不能断；or 会全扫，% 左会完蛋；类型要匹配，函数别乱算；满足这几条，索引跑满速。**





## 二,AND NOT EXISTS

### 1️⃣ 基本概念

`NOT EXISTS` 是 SQL 的一种子查询判断方式，用于判断**子查询是否没有返回任何记录**。

语法示例：

```mysql
SELECT *
FROM table1 t1
WHERE NOT EXISTS (
    SELECT 1
    FROM table2 t2
    WHERE t2.col = t1.col
);
```

意思是：

> 只返回 `table1` 中的记录，这些记录在 `table2` 中找不到对应的匹配。

**关键点：**

- `EXISTS` / `NOT EXISTS` 子查询通常与外层查询有相关列（correlated column）。
- 子查询只需判断是否存在匹配，不关心具体返回的内容，所以常写 `SELECT 1`。
- `NOT EXISTS` 用于排除匹配数据。

### 2️⃣ 简单案例

假设有两个表：

**users 表**

| user_id | name  |
| ------- | ----- |
| 1       | Alice |
| 2       | Bob   |
| 3       | Carol |

**orders 表**

| order_id | user_id | product |
| -------- | ------- | ------- |
| 101      | 1       | A       |
| 102      | 2       | B       |

如果想找出 **没有下过订单的用户**，可以用：

```mysql
SELECT *
FROM users u
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.user_id = u.user_id
);
```

查询结果：

| user_id | name  |
| ------- | ----- |
| 3       | Carol |

解释：

- `u.user_id = 3` 的 Carol 在 `orders` 中找不到对应 `user_id`。
- 所以 `NOT EXISTS` 条件为真，返回 Carol。

### 3️⃣ 常见使用场景

1. **查找缺失或未关联的数据**
   - 用户未下单、学生未选课、员工未参加培训等。
   - 比 `LEFT JOIN ... WHERE t2.col IS NULL` 更安全，因为不会被重复行影响。
2. **复杂逻辑排除**
   - 当排除条件涉及多列或复杂筛选时，`NOT EXISTS` 能高效处理。
3. **性能优化**
   - 对大表关联，`NOT EXISTS` 通常比 `NOT IN` 性能更好，尤其是子查询列可能为 NULL 时。
   - `NOT IN` 在子查询结果有 NULL 时可能产生意外结果。

### 4️⃣ 进阶案例

找出 **2025-09 月未完成订单的用户**：

```mysql
SELECT *
FROM users u
WHERE NOT EXISTS (
    SELECT 1
    FROM orders o
    WHERE o.user_id = u.user_id
      AND o.status = 'completed'
      AND o.order_date >= '2025-09-01'
      AND o.order_date < '2025-10-01'
);
```

- 这里我们用 `NOT EXISTS` 精确排除了指定时间段内已完成订单的用户。
- 外层查询只返回未完成订单的用户。

------

✅ **总结要点**

| 特性                       | 说明                                             |
| -------------------------- | ------------------------------------------------ |
| 主要作用                   | 排除子查询有结果的外层行                         |
| 典型场景                   | 未关联、未完成、缺失、排除某类数据               |
| 与 `LEFT JOIN ... IS NULL` | 功能类似，但更安全、高效，避免重复行导致结果膨胀 |
| 与 `NOT IN` 区别           | `NOT IN` 对 NULL 敏感，`NOT EXISTS` 更稳妥       |

###  实际案例场景演示

#### 数据表

```mysql
-- 原sql查询0.7秒
( SELECT
	`my`.`head`,
	sum(
		IF
		( my.is_show = 0, 1, 0 )) AS my_count,
		sum(
			my.create_time >= '2025-09-01' 
			AND my.create_time < '2025-10-01' 
		AND NOT EXISTS ( SELECT 1 FROM pxs_gw_pool g WHERE g.message_no = my.message_no AND g.message_no <> '' )) AS my_create 
	FROM
		`pxs_message_my` `my` 
	WHERE
		`my`.`delete_time` IS NULL 
	GROUP BY
	`head` 
	)
	
	
	
	-- 修改后 0.2秒
	( SELECT
	`my`.`head`,
	sum(
		IF
		( my.is_show = 0, 1, 0 )) AS my_count,
		sum(
			my.create_time >= '2025-09-01' 
			AND my.create_time < '2025-10-01' 
		AND NOT EXISTS ( SELECT 1 FROM pxs_gw_pool g WHERE g.message_no = my.message_no AND g.message_no <> '' )) AS my_create 
	FROM
		`pxs_message_my` `my` 
	WHERE
		`my`.`delete_time` IS NULL 
	GROUP BY
	`head` 
	)
```





