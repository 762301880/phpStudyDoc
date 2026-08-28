## 参考资料	

| 第三方博客                                   | 地址                                                         |
| -------------------------------------------- | ------------------------------------------------------------ |
| 个人博客( leftjoin 之后如何获取最后一条数据) | [link](https://gitee.com/yaolliuyang/phpStudyDoc/blob/main/mysql/%E5%BC%80%E5%8F%91%E9%81%87%E5%88%B0%E9%9A%BE%E9%A2%98%E8%AE%B0%E5%BD%95.md#leftjoin---%E4%B9%8B%E5%90%8E%E5%A6%82%E4%BD%95%E8%8E%B7%E5%8F%96%E6%9C%80%E5%90%8E%E4%B8%80%E6%9D%A1%E6%95%B0%E6%8D%AE) |

## 数据表

```sql
-- 用户表
CREATE TABLE `sys_user` (
  `id` BIGINT NOT NULL COMMENT '用户主键id',
  `user_name` VARCHAR(50) NOT NULL COMMENT '用户名',
  `age` INT DEFAULT NULL COMMENT '年龄',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='用户主表';

-- 订单表，一个用户有多条订单记录
CREATE TABLE `sys_order` (
  `id` BIGINT NOT NULL COMMENT '订单主键id',
  `user_id` BIGINT NOT NULL COMMENT '关联用户id',
  `order_name` VARCHAR(100) NOT NULL COMMENT '订单名称',
  `create_time` DATETIME NOT NULL COMMENT '下单时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='订单从表';
```

**插入数据**

```sql
-- 插入用户
INSERT INTO sys_user(id,user_name,age) VALUES
(1,'张三',22),
(2,'李四',25),
(3,'王五',30);

-- 插入订单
INSERT INTO sys_order(id,user_id,order_name,create_time) VALUES
(1,1,'订单A','2026-01-01 10:00:00'),
(2,1,'订单B','2026-02-01 10:00:00'),
(3,1,'订单C','2026-03-01 10:00:00'),
(4,2,'订单X','2026-01-05 10:00:00'),
(5,2,'订单Y','2026-02-05 10:00:00');
```

## 查询sql示例

### MySQL8.0+ 窗口函数（推荐，最稳定）

```sql
SELECT
    u.id,
    u.user_name,
    u.age,
    o.id AS order_id,
    o.order_name,
    o.create_time
FROM sys_user u
LEFT JOIN (
    SELECT 
        *,
        ROW_NUMBER() OVER(PARTITION BY user_id ORDER BY create_time DESC) rn
    FROM sys_order
) o 
ON u.id = o.user_id AND o.rn = 1;
```

### MySQL5.7 无窗口函数版本（老数据库）

```sql
SELECT
    u.id,
    u.user_name,
    u.age,
    o.id AS order_id,
    o.order_name,
    o.create_time
FROM sys_user u
LEFT JOIN sys_order o 
    ON u.id = o.user_id
    AND o.create_time = (
        SELECT MAX(create_time) 
        FROM sys_order 
        WHERE user_id = u.id
    );
```

### 按最大 id 取最新（解决多条记录时间相同重复问题）

```sql
SELECT
    u.id,
    u.user_name,
    u.age,
    o.id AS order_id,
    o.order_name,
    o.create_time
FROM sys_user u
LEFT JOIN (
    SELECT user_id,MAX(id) maxid 
    FROM sys_order 
    GROUP BY user_id
) t ON u.id = t.user_id
LEFT JOIN sys_order o ON o.id = t.maxid;
```



