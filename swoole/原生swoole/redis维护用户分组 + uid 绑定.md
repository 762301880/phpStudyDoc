#  用redis维护用户分组 + uid 绑定

 Redis 来维护 **Swoole 用户分组 + uid 绑定关系**是非常常见，适合多进程 / 多服务器场景。

## 需求本质

一般是在做类似：

- WebSocket / TCP server（Swoole）
- 用户登录后有 `uid`
- 用户可能加入多个“组”（room / channel / guild）
- 需要实现：
  - uid ↔ fd（连接）
  - uid ↔ group（分组）
  - group ↔ 多个 uid

##  推荐的 Redis 设计方式

### 1. uid ↔ fd（连接映射）

```bash
uid:1001 -> fd:12
fd:12   -> uid:1001
```

Redis key：

```bash
ws:uid:1001 -> 12
ws:fd:12 -> 1001
```

用 String

### 2. 用户加入的分组（重点）

#### uid 属于哪些 group：

```
ws:user:1001:groups = {group1, group2}
```

👉 用 Set

```
SADD ws:user:1001:groups group1
SADD ws:user:1001:groups group2
```

------

#### group 下有哪些用户：

```
ws:group:group1 = {1001, 1002, 1003}
```

👉 也是 Set

```bash
SADD ws:group:group1 1001
SADD ws:group:group1 1002
```

## 发送消息时的流程

### 群发 group 消息：

1. `SMEMBERS ws:group:group1`
2. 拿到所有 uid
3. uid → fd
4. `pushToFd(fd, message)`

## 为什么 Redis 适合

### 优点：

- ✔ 分布式共享（多台 Swoole server 都能读）
- ✔ Set 天然去重（避免重复用户）
- ✔ O(1) 增删
- ✔ 支持高并发

## 需要注意的坑（很关键）

### 1. 断线清理

fd 断开必须清理：

```
DEL ws:fd:12
DEL ws:uid:1001
SREM ws:group:xxx 1001（可选）
```

否则会“僵尸用户”

### 2. 心跳机制

建议：

- 定时 heartbeat
- 超时清理 Redis 映射

### 多 server 问题

如果你是：

- Swoole cluster
- 或多进程 + 多机器

  Redis 是必须的，不然 fd 映射会乱

### 大规模优化（用户很多时）

如果 group 很大（比如 10w 人）：

可以优化成：

- 分片 group
- 或用 Redis Stream / MQ 推送
- 或本地缓存 + Redis sync

## 进阶一点的设计

可以这样拆：

```shell
Redis
├── ws:uid:fd
├── ws:fd:uid
├── ws:user:groups
├── ws:group:users
└── ws:online:uids（在线集合）
```





