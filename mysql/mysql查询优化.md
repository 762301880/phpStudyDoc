## mysql查询优化

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

## 联合索引

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