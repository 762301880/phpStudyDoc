## TypePHP 介绍

### TypePHP（Swoole 团队新项目）

> 不是网页框架！是 **PHP 编译器**（原名叫 Swoole‑AOT）
>
> 简单大白话：
>
> 普通 PHP：`.php`源码交给 php.exe 解释运行，速度一般，源码明文可以被看到。
>
> **TypePHP：把你的 PHP 代码，编译成独立的 exe 二进制程序**，不再依赖 php 环境运行，速度大幅提升，还能保护源码不泄露。

### TypePHP 能干啥

1. 把 PHP 代码编译成**独立可执行文件**（Windows exe、Linux 程序），服务器不用装 PHP 也能跑；
2. 性能暴涨，适合计算密集任务；
3. 编译之后看不到源代码，做商业软件保护版权；
4. 可以调用 C/C++ 底层库Swoole。

## 资料

| 名称                   | 地址                                               |
| ---------------------- | -------------------------------------------------- |
| typephp-swoole官方文档 | [link](https://www.swoole.com/aot/zh/docs/install) |

## 安装

### windows环境

> 从 https://github.com/swoole/typephp/releases 下载 `TypePHP Windows` 工具包，解压至 `D:\workspace\typephp-windows-x64`
>
> [tpc_v0.7.0_windows_x64.zip](https://github.com/swoole/typephp/releases/download/v0.7.0/tpc_v0.7.0_windows_x64.zip)

**环境变量**

- PHP_HOME=D:\workspace\typephp-windows-x64
- PHPX_HOME=D:\workspace\typephp-windows-x64\phpx
- Path+=D:\workspace\typephp-windows-x64

`TypePHP Windows` 软件包中包含完整版的 PHP 8.4 ZTS、PHPX、本地 DLL 和 `tpc.exe`。可以通过 `php.ini` 加载工具包中提供的更多 PHP 扩展。