## GatewayWorker资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| GatewayWorker官方文档 | [link](https://www.workerman.net/doc/gateway-worker/README.html) |
| 博客pdf               | [pdf](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/ThinkPHP%205.1%20Workerman%20%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20ThinkPHP5.1%20Workerman%E4%B8%8A%E6%89%8B%E6%8C%87%E5%8D%97%20%C2%B7%20%E7%9C%8B%E4%BA%91.pdf) |

## [tp下安装GatewayWorker](https://www.kancloud.cn/manual/thinkphp5_1/354134)

### 1. 直接安装封装好的扩展库

**安装服务端**

> composer安装

```php
composer require topthink/think-worker=2.0.*
```

**对应的配置文件解析**

> 这三个配置文件都属于 GatewayWorker 扩展包，用于实现 WebSocket 和 TCP 长连接服务的配置。
>
> - `gateway_worker.php` 是 GatewayWorker 服务的核心配置文件，它定义了服务的各种参数，如监听的 IP 和端口、进程数、Worker 和 Gateway 的运行模式、协议类型等。
> - `worker.php` 是 Worker 进程的配置文件，用于设置 Worker 进程相关的参数，例如进程的名称、任务处理逻辑、定时任务、各种事件的处理等。
> - `worker_server.php` 是 WorkerServer 进程的配置文件，通常用于设置服务器的一些基本参数，例如监听的 IP 和端口、WorkerServer 的运行模式等。

这三个配置文件中定义了不同层次的配置参数，通过它们，我们可以很方便地对 GatewayWorker 的各项功能和性能进行配置，并启动和管理相关的进程。