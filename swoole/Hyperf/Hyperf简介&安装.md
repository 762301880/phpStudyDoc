# 简介

> Hyperf 是基于 `Swoole 4.5+` 实现的高性能、高灵活性的 PHP 协程框架，内置协程服务器及大量常用的组件，性能较传统基于 `PHP-FPM` 的框架有质的提升，提供超高性能的同时，也保持着极其灵活的可扩展性，标准组件均基于 [PSR 标准](https://www.php-fig.org/psr) 实现，基于强大的依赖注入设计，保证了绝大部分组件或类都是 `可替换` 与 `可复用` 的。
>
> 框架组件库除了常见的协程版的 `MySQL 客户端`、`Redis 客户端`，还为您准备了协程版的 `Eloquent ORM`、`WebSocket 服务端及客户端`、`JSON RPC 服务端及客户端`、`GRPC 服务端及客户端`、`Zipkin/Jaeger (OpenTracing) 客户端`、`Guzzle HTTP 客户端`、`Elasticsearch 客户端`、`Consul 客户端`、`ETCD 客户端`、`AMQP 组件`、`Apollo 配置中心`、`阿里云 ACM 应用配置管理`、`ETCD 配置中心`、`基于令牌桶算法的限流器`、`通用连接池`、`熔断器`、`Swagger 文档生成`、`Swoole Tracker`、`视图引擎`、`Snowflake 全局 ID 生成器` 等组件，省去了自己实现对应协程版本的麻烦。
>
> Hyperf 还提供了 `基于 PSR-11 的依赖注入容器`、`注解`、`AOP 面向切面编程`、`基于 PSR-15 的中间件`、`自定义进程`、`基于 PSR-14 的事件管理器`、`Redis/RabbitMQ 消息队列`、`自动模型缓存`、`基于 PSR-16 的缓存`、`Crontab 秒级定时任务`、`国际化`、`Validation 表单验证器` 等非常便捷的功能，满足丰富的技术场景和业务场景，开箱即用。

## 资料

| 名称              | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| Hyperf文档        | [跳转地址](https://hyperf.wiki/2.1/#/)                       |
| B站hyperf视频教程 | [跳转地址](https://www.bilibili.com/video/BV1WZ4y1x7Hw?p=1)  |
| 第三方Hyperf文档  | [跳转地址](https://itdashu.com/docs/hyperf/c5ee6/db-paginator.html) |

## 创建项目

**[通过 `Composer` 创建项目](https://www.hyperf.wiki/2.1/#/zh-cn/quick-start/install?id=通过-composer-创建项目)**

> 接下来composer安装Hyperf的骨架项目（可能会提示proc_open等函数被禁用，所以提前去除这些禁用再安装）：composer create-project hyperf/hyperf-skeleton hyperf，安装过程会提示按需选择安装hyperf提供的组件，我们可以全选n选项，或者按自己需要的选择安装；
>
> 安装过程中的选项我们全部选择n，

```php
composer create-project hyperf/hyperf-skeleton  + 项目名称
```

# docker启动Hyperf

> **注意容器外部的本地端口一定要映射 `hyperf` 内部的访问端口(及hyperfHttp开启地址) **
>
> 项目 启动 Hyperf 之后才可以 本地+端口访问

**参考资料**

| 名称       | 地址                                                 |
| ---------- | ---------------------------------------------------- |
| 第三方博客 | [link](https://www.freesion.com/article/3230360618/) |

##  官方[Docker 下开发](https://www.hyperf.wiki/2.0/#/zh-cn/quick-start/install?id=docker-下开发)

- [地址](https://www.hyperf.wiki/2.0/#/zh-cn/quick-start/install?id=%E9%80%9A%E8%BF%87-composer-%E5%88%9B%E5%BB%BA%E9%A1%B9%E7%9B%AE)

假设您的本机环境并不能达到 Hyperf 的环境要求，或对于环境配置不是那么熟悉，那么您可以通过以下方法来运行及开发 Hyperf 项目：

```php
# 下载并运行 hyperf/hyperf 镜像，并将镜像内的项目目录绑定到宿主机的 /tmp/skeleton 目录
docker run -itd  -v /tmp/skeleton:/hyperf-skeleton -p 9501:9501 -p 2021:80 --restart=always  --name 自定义名称 --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole

# 将 Composer 镜像设置为阿里云镜像，加速国内下载速度
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer

# 通过 Composer 安装 hyperf/hyperf-skeleton 项目
composer create-project hyperf/hyperf-skeleton

# 进入安装好的 Hyperf 项目目录
cd hyperf-skeleton
# 启动 Hyperf
php bin/hyperf.php start
```

接下来，就可以在 `/tmp/skeleton` 中看到您安装好的代码了。由于 Hyperf 是持久化的 CLI 框架，当您修改完您的代码后，通过 `CTRL + C` 终止当前启动的进程实例，并重新执行 `php bin/hyperf.php start` 启动命令即可。

## helloword

> 在打开的浏览器中输入`http://127.0.0.1:9501/`

```php
method	"GET"
message	"Hello Hyperf."
```

#  遇到的bug

## 端口访问问题

![lALPDg7mSNyFfUzNAkvNA-w_1004_587.png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1lyxkrdUbHPI6u3.png)

## **swoole配置问题**

````php
# php bin/hyperf.php start >> hyperf_start_run.log  #将启动输出的结果保存到日志
root@f137b92f2b16:/www/hyperf-skeleton# php bin/hyperf.php start
````

如果出现以下bug解决方法

```php
ERROR Swoole short name have to disable before start server, please set swoole.use_shortname = off into your php.ini.
```

**编辑php.ini **

```php
 cd /usr/local/etc/php&&vim php.ini
 # 在文件中加入
 extension=swoole.so
 swoole.use_shortname = off
```

