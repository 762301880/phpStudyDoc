# 简介

> GatewayWorker是基于Workerman开发的	
>
> GatewayWorker基于Workerman开发的一个项目框架，用于快速开发长连接应用，例如app推送服务端、即时IM服务端、游戏服务端、物联网、智能家居等等。
> GatewayWorker使用经典的Gateway和Worker进程模型。Gateway进程负责维持客户端连接，并转发客户端的数据给Worker进程处理；Worker进程负责处理实际的业务逻辑，并将结果推>送给对应的客户端。Gateway服务和Worker服务可以分开部署在不同的服务器上，实现分布式集群。
> GatewayWorker提供非常方便的API，可以全局广播数据、可以向某个群体广播数据、也可以向某个特定客户端推送数据。配合Workerman的定时器，也可以定时推送数据。

- 参考资料

| 名称                      | 地址                                            |
| ------------------------- | ----------------------------------------------- |
| GatewayWorker2.x 3.x 手册 | [链接](http://doc2.workerman.net/)              |
| 安装文档                  | [链接](https://github.com/walkor/GatewayClient) |

## 安装

>安装内核
>只安装GatewayWorker内核文件（不包含start_gateway.php start_businessworker.php等启动入口文件）

- 使用`composer`在项目中安装

```shell
composer require workerman/gatewayclient
```

# 使用

