# 说明





# 二、安装&使用

## 2.1需要在[官网](https://pusher.com/)注册 key&app_id&secret

## <img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210623094627129.png" alt="image-20210623094627129" style="zoom: 50%;" /> 

## 2.2安装

### 2.2.1使用composer安装

- 项目`packagist`[地址](https://packagist.org/packages/pusher/pusher-php-server)

```php
composer require pusher/pusher-php-server
```

- 项目文档[地址](https://packagist.org/packages/pusher/pusher-php-server)

## 2.3 简单使用示例

- 控制器代码

```php
$app_id = 'YOUR_APP_ID';
$app_key = 'YOUR_APP_KEY';
$app_secret = 'YOUR_APP_SECRET';
$app_cluster = 'YOUR_APP_CLUSTER';
$pusher = new Pusher($app_key,$app_secret,$app_id,['cluster' => $app_cluster]);# 实例化pusher
```

- new Puher();参数详解

```php
#第四个参数是一个$options数组。附加选项是：
scheme - #例如 http 或 https
host- #主机，例如 api.pusherapp.com。没有尾随正斜杠
port - #http 端口
path- #附加到所有请求路径的前缀。这仅在您针对您自己控制的端点（例如，基于路径前缀路由的代理）运行库时才有用。
timeout - #HTTP 超时
useTLS - #使用 https 和端口 443 方案的快速选项。
cluster - #指定运行应用程序的集群。
encryption_master_key- #一个 32 字符长的密钥。此密钥与通道名称一起用于派生每个通道的加密密钥。每通道密钥用于加密加密通道上的事件数据。
```

