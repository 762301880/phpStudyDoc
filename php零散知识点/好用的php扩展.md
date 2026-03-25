## OPCache

OPcache 是 PHP 官方内置的**字节码缓存扩展**，能把 PHP 源码编译成字节码缓存到内存，大幅提升 PHP 执行效率（性能提升 3~10 倍），**PHP 5.5+ 已内置，无需额外下载源码**。

### 检查是否已安装

先执行命令查看 OPcache 是否存在

```bash
php -m | grep opcache
# 或者创建 phpinfo.php 查看
<?php phpinfo(); ?>
```

输出 `opcache` = 已安装

无输出 = 未启用，需要配置

### 安装 / 启用 OPcache

1. PHP 5.5+/7+/8+（官方内置）

**无需下载，直接修改 php.ini 启用**

找到 php.ini 路径（执行命令查看）：

```bash
php --ini
```

1. 编辑 php.ini，添加 / 修改以下配置：

   ```ini
   ; 启用 OPcache
   opcache.enable=1
   ; CLI 模式下启用（命令行执行 PHP 也生效）
   opcache.enable_cli=1
   ; 共享内存大小（推荐 128/256MB，根据服务器内存调整）
   opcache.memory_consumption=128
   ; 最大缓存文件数（根据项目文件量调整，小项目 4000 足够）
   opcache.max_accelerated_files=10000
   ; 自动刷新缓存（开发环境开，生产环境关）
   opcache.validate_timestamps=1
   ; 缓存过期时间（秒），开发环境设 2，生产环境设 0
   opcache.revalidate_freq=2
   ; 开启缓存注释（框架/依赖需要注释，必须开）
   opcache.save_comments=1
   ; 开启快速关闭
   opcache.fast_shutdown=1
   ```

2. 重启 PHP 服务（关键！）：

   ```bash
   # Nginx + PHP-FPM
   systemctl restart php-fpm
   # Apache
   systemctl restart httpd
   ```

2. 宝塔面板（一键启用）

1. 进入「软件商店」→ 找到你的 PHP 版本 →「设置」
2. 「安装扩展」→ 找到 `opcache` → 一键安装
3. 「配置修改」→ 调整参数 → 保存重启

核心配置参数说明（生产 / 开发环境区分）

### 使用与管理 OPcache

### 1. 手动清空缓存（生产环境必备）

生产环境关闭了自动刷新，**修改代码后必须清空缓存**才能生效：

#### 方法 1：重启 PHP 服务（最简单）

```bash
systemctl restart php-fpm
```

#### 方法 2：代码清空（无感知重启）

创建 `opcache_reset.php`，访问一次即可清空：

```php
<?php
// 清空 OPcache 缓存
opcache_reset();
echo "OPcache 缓存已清空";
// 用完立即删除！防止安全风险
unlink(__FILE__);
?>
```

### 2. 查看缓存状态（可视化面板）

推荐使用 **opcache-gui** 工具，直观查看缓存命中率、内存使用：

1. 下载：https://github.com/amnuts/opcache-gui
2. 上传到网站目录，直接访问即可
3. 核心指标：**缓存命中率 > 95% 为正常**

### 3. 代码中查看状态

```php
<?php
// 获取 OPcache 状态信息
$status = opcache_get_status();
echo "缓存命中率：" . round($status['opcache_statistics']['hit_rate'] ,2) . "%";
echo "<br>已缓存文件：" . $status['opcache_statistics']['num_cached_scripts'];
```

### 常见问题

1. 修改代码不生效？

   生产环境 

   ```
   opcache.validate_timestamps=0
   ```

    关闭了自动刷新，

   重启 PHP 或执行 opcache_reset ()

    即可。

2. 缓存命中率低？

   调大 

   ```
   opcache.memory_consumption
   ```

    和 

   ```
   opcache.max_accelerated_files
   ```

3. PHP 启动失败？

   检查 php.ini 中是否重复配置 opcache，删除重复配置即可。