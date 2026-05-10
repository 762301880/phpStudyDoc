##  hyperf  2.2版本

> [参考官方文档](https://hyperf.wiki/2.2/#/zh-cn/swoole-tracker?id=%e5%ae%89%e8%a3%85%e6%89%a9%e5%b1%95)

### 目录结构

```php
# 项目目录下创建
Dockerfile
docker-compose.yml
docker-entrypoint.sh    
```

### Dockerfile

```dockerfile
FROM hyperf/hyperf:7.4-alpine-v3.11-swoole

ARG timezone
ENV TIMEZONE=Asia/Shanghai \
    APP_ENV=dev \
    SCAN_CACHEABLE=false

RUN set -ex \
    && cd /etc/php7 \
    && echo "date.timezone=${TIMEZONE}" > conf.d/99_overrides.ini \
    && ln -sf /usr/share/zoneinfo/${TIMEZONE} /etc/localtime \
    && echo "${TIMEZONE}" > /etc/timezone

WORKDIR /opt/www

COPY docker-entrypoint.sh /entrypoint.sh
RUN chmod +x /entrypoint.sh

# 🔥 同时暴露 9551 - 9554 端口
EXPOSE 9501 9502 9503 9504

ENTRYPOINT ["/entrypoint.sh"]
```

### docker-compose.yml

```dockerfile
version: '3'

services:
  hyperf:
    build: .
    container_name: hyperf2.2
    ports:
      - "9551:9501"
      - "9552:9502"
      - "9554:9504" #jsonrpc
    volumes:
      - .:/opt/www
      # 挂载启动日志 
      - ./runtime/start/logs:/var/www/html/runtime/logs/
    environment:
      - APP_ENV=dev
      - SCAN_CACHEABLE=false
    tty: true
    restart: always
```



### docker-entrypoint.sh 

```shell
#!/bin/sh
set -e

echo "📦 Checking composer dependencies..."

if [ ! -f "vendor/autoload.php" ]; then
    echo "⚠️ vendor not ready, running composer install..."
    composer install --no-interaction --prefer-dist --optimize-autoloader
else
    echo "✅ composer dependencies already installed"
fi

## 💥 关键：清理缓存（必须加）
#echo "🧹 Clearing runtime cache..."
#rm -rf runtime/container
#rm -rf runtime/annotations

# 定义日志文件路径
LOG_FILE="/var/www/html/runtime/logs/start.log"

mkdir -p /var/www/html/runtime/logs/

echo "🚀 Starting Hyperf... Logs will be written to $LOG_FILE"

exec php bin/hyperf.php start >> "$LOG_FILE" 2>&1
```

