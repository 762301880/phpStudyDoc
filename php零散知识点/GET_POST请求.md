## 问什么这个要单独放出来说

> 因为这里面学问大着呢

##  无法获取上传的大文件

> 今天需要分片解析上传的txt文档  文档大小16M 但是用thinkphp file就是无法获取到文件 上传小图片就可以获取到

**问题原因**

#### 一、文件大小超限（最高概率，你的文件 16MB） 

##### 1. 修改 php.ini 上传配置

找到 php.ini，修改两处，数值大于你的文件大小（建议 30M 起步）

```php
; 单次单文件最大
upload_max_filesize = 30M
; 整个POST请求总大小（必须比upload大）
post_max_size = 35M
```

#####  2. Nginx 拦截大请求（90% 本地 / 线上部署漏配）

打开站点 nginx 配置，在 server 块内添加：

```php
client_max_body_size 30m;
```

重载 nginx 生效：

```bash
nginx -s reload
```

> 原理：nginx 默认限制很小，超过会直接截断请求，PHP 收不到任何数据，$_FILES 为空。

