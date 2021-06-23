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

## 2.3 触发事件示例

> 要在一个或多个通道上触发事件，请使用该`trigger`函数。

> ### 单通道
>
> ```php
> #my-channel=>频道,my_event=>事件,data=>数据:可以是json数据 
> pusher -> trigger ( 'my-channel' , 'my_event' , 'hello world' );
> ```
>
> ### 多渠道
>
> ```php
>  pusher -> trigger ([ 'channel-1' , 'channel-2' ], 'my_event' , 'hello world' );
> ```



- 发送完成之后可以在控制台查看输出的内容

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210623105910893.png" alt="image-20210623105910893" style="zoom:50%;" />

## 2.4前端接受事件

- 可以在后台[入门](https://dashboard.pusher.com/apps/1222147/getting_started)这里查询对应的前端代码，更多方式请查阅官网[文档](https://pusher.com/docs)

```js
<!DOCTYPE html>
<head>
  <title>Pusher Test</title>
  <script src="https://js.pusher.com/7.0/pusher.min.js"></script>
  <script>

    // Enable pusher logging - don't include this in production
    Pusher.logToConsole = true;

    var pusher = new Pusher('add71346f11ab0277158', {
      cluster: 'mt1'
    });

    var channel = pusher.subscribe('my-channel');
    channel.bind('my-event', function(data) {
      alert(JSON.stringify(data));
    });
  </script>
</head>
<body>
  <h1>Pusher Test</h1>
  <p>
    Try publishing an event to channel <code>my-channel</code>
    with event name <code>my-event</code>.
  </p>
</body>
```

