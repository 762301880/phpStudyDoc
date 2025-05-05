



# 下载脚本



https://gitee.com/yao_liuyang/linux_shell/blob/master/redis_backup.sh



# 代码备份

```shell
#!/bin/bash

# Redis自动备份脚本

# 配置参数
REDIS_CLI="/usr/bin/redis-cli"    # redis-cli路径
REDIS_RDB_DIR="/var/lib/redis"    # Redis RDB文件存储目录
BACKUP_DIR="/data/backups/redis"  # 备份存储目录
BACKUP_KEEP_DAYS=7                # 保留备份天数
COMPRESS=true                     # 是否压缩备份文件
LOG_FILE="/var/log/redis_backup.log"  # 日志文件路径

# 创建备份目录（如果不存在）
mkdir -p ${BACKUP_DIR}

# 日志记录函数
log() {
    local timestamp=$(date "+%Y-%m-%d %H:%M:%S")
    echo "[${timestamp}] $1" >> ${LOG_FILE}
}

# 检查Redis RDB文件是否存在
check_redis_files() {
    if [ ! -f "${REDIS_RDB_DIR}/dump.rdb" ]; then
        log "错误：未找到Redis RDB文件"
        exit 1
    fi
}

# 执行备份
perform_backup() {
    local current_time=$(date "+%Y%m%d-%H%M%S")
    local backup_name="redis_backup_${current_time}"
    local backup_path="${BACKUP_DIR}/${backup_name}"
    
    # 触发Redis后台保存
    ${REDIS_CLI} bgsave >/dev/null
    
    # 等待保存完成（最多等待60秒）
    local counter=0
    while [ ${counter} -lt 60 ]; do
        if ${REDIS_CLI} --raw lastsave | grep -q $(date +%s); then
            break
        fi
        sleep 1
        ((counter++))
    done

    # 复制RDB文件
    cp "${REDIS_RDB_DIR}/dump.rdb" "${backup_path}.rdb"
    
    # 压缩备份文件
    if [ ${COMPRESS} = true ]; then
        gzip -f "${backup_path}.rdb"
        backup_file="${backup_path}.rdb.gz"
    else
        backup_file="${backup_path}.rdb"
    fi
    
    log "备份创建成功：${backup_file}"
}

# 清理旧备份
clean_old_backups() {
    find ${BACKUP_DIR} -name 'redis_backup_*' -mtime +${BACKUP_KEEP_DAYS} -exec rm -f {} \;
    log "已清理超过${BACKUP_KEEP_DAYS}天的旧备份"
}

# 主程序
log "========== 开始Redis备份 =========="
check_redis_files
perform_backup
clean_old_backups
log "备份流程完成"
```

**解压备份的文件**

```sh
gunzip redis_backup_20250430-162026.rdb.gz
```



**使用说明：**

1. 根据你的系统环境修改以下参数：
   - `REDIS_CLI`: redis-cli的完整路径
   - `REDIS_RDB_DIR`: Redis实际的RDB文件存储目录
   - `BACKUP_DIR`: 备份文件存储目录
   - `BACKUP_KEEP_DAYS`: 需要保留备份的天数
   - `COMPRESS`: 是否启用GZIP压缩
2. 给脚本添加执行权限

 ```bash
chmod +x redis_backup.sh
 ```

3. 添加定时任务（每日凌晨2点执行）：

 ```shell
crontab -e
 ```

添加以下内容：

```shell
0 2 * * * /path/to/redis_backup.sh
```

**功能说明：**

1. 自动触发Redis后台保存（BGSAVE）
2. 等待保存完成（最多等待60秒）
3. 自动压缩备份文件（可选）
4. 自动清理旧备份文件
5. 详细的日志记录
6. 错误检查机制

**注意事项：**

1. 确保运行脚本的用户对Redis目录和备份目录有读写权限
2. 建议提前测试脚本的备份恢复流程
3. 如果Redis使用不同端口或密码，需要修改redis-cli命令参数
4. 首次使用建议手动运行测试：

# 备份注意事项

## redis 版本需一致(重要)

> Redis 的 RDB 备份文件（`.rdb`）**可以直接跨系统导入**，但需要满足以下条件和操作步骤

### **关键条件**

1. **Redis 版本兼容性**
   - A 系统和 B 系统的 Redis **主版本号必须一致**（例如均为 6.x 或 7.x）。
   - 如果版本不一致，高版本 Redis 可能无法加载低版本的 RDB 文件（可通过 `redis-server --version` 检查）。
2. **数据覆盖风险**
   - 导入 RDB 文件会 **完全覆盖** B 系统 Redis 的当前数据，请确保 B 系统的数据已备份或无价值

### **操作步骤**

#### 方法 1：直接替换 RDB 文件（推荐）

1. **停止 B 系统的 Redis 服务**：

   ```bash
   redis-cli shutdown save  # 安全停止并保存当前数据（可选）
   ```

2. **找到 B 系统的 RDB 文件路径**：
   查看 Redis 配置文件 `redis.conf` 中的 `dir` 和 `dbfilename` 参数：

   ```bash
   grep -E "dir|dbfilename" /path/to/redis.conf
   ```

   默认路径通常是：

   - Linux: `/var/lib/redis/dump.rdb`
   - Windows: `C:\Redis\dump.rdb`

3. **替换 RDB 文件**：

   - 删除或备份 B 系统原有的 `dump.rdb` 文件。
   - 将 A 系统的备份文件（如 `redis_backup_20250430-162026.rdb`）重命名为 `dump.rdb`，并复制到 B 系统的 RDB 目录。

1. **重启 Redis 服务**：

   ```bash
   redis-server /path/to/redis.conf
   ```

   Redis 会自动加载新的 RDB 文件，数据即生效。

### **注意事项**

1. **跨版本迁移风险**
   - 若 A 系统的 Redis 版本 > B 系统，直接替换 RDB 文件可能导致数据损坏。建议升级 B 系统的 Redis 版本后再操作。
2. **内存容量**
   - 确保 B 系统的 Redis 内存容量足够容纳导入的数据。
3. **替代方案**
   - 主从同步：将 B 系统配置为 A 系统的从库，通过 `REPLICAOF` 命令同步数据。
   - 在线迁移工具：使用 [`redis-shake`](https://github.com/alibaba/RedisShake) 或 [`redis-migrate`](https://github.com/tkuchiki/redis-migrate)。