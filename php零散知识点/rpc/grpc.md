## 安装grpc扩展

### windows

####  注意:

> grpc 从`1.44.0`以后，PECL 不再产出 PHP7.4 的 Windows 二进制包

#### php7.4.3

[grpc扩展-grpc-1.43.0](https://pecl.php.net/package/gRPC/1.43.0/windows)  [点击下载](https://downloads.php.net/~windows/pecl/releases/grpc/1.43.0/php_grpc-1.43.0-7.4-nts-vc15-x64.zip)

解压后得到：

1. `php_grpc.dll` → 放到 php 的`ext`文件夹
2. `grpc.dll` → **放到 php 根目录（和 php.exe 同一级，不要放 ext！）**

php.ini 添加

```ini
extension=grpc
```

#### [protobuf 扩展](https://pecl.php.net/package/protobuf)

**Protobuf（Protocol Buffers）是谷歌的序列化工具**，简单理解：

> JSON/XML 是文本格式；protobuf 是**二进制序列化格式**。

- JSON：人能看懂文本，体积大、解析慢。
- Protobuf：二进制，**体积更小、编解码速度更快**，gRPC 默认就用它做数据传输。

`.proto` 文件就是它的接口定义文件，描述请求、响应字段。 通过 `protoc` 工具可以把 proto 编译成各语言代码（PHP/Go/Java）。

> gRPC ≠ protobuf；gRPC 是 RPC 通信框架，**默认绑定 protobuf 做序列化**。

**安装**

> 同样选：php7.4 NTS x64，推荐版本 [**3.24.4**](https://pecl.php.net/package/protobuf/3.24.4/windows)（PHP7.4 兼容稳定版）PECL 下载：https://pecl.php.net/package/protobuf/3.24.4/windows

- `php_protobuf.dll` → ext 目录
- php.ini 增加：

```ini
extension=protobuf
```

### linux

**安装 PHP gRPC 扩展**

```bash
# pecl安装
pecl install grpc
pecl install protobuf
```

编辑`php.ini`

```ini
extension=grpc.so
extension=protobuf.so
```

验证

```bash
php -m | grep grpc
php -m | grep protobuf
```



---



## protoc 编译器(开发环境必须安装)

> 一句话：**protoc = 根据 proto 文件，帮你手写一大堆 PHP 模型和客户端代码。**
>
> 用于生成 PHP 代码（仅开发阶段使用，线上运行不需要 protoc）

### [windows](https://github.com/protocolbuffers/protobuf/releases#release-v35.1)	

[下载](https://github.com/protocolbuffers/protobuf/releases/download/v35.1/protoc-35.1-win64.zip)

解压后把 `bin/protoc.exe` 放到 `C:\tools\protoc\bin`，把这个目录加入系统环境变量 PATH。 cmd 执行：

```bash
protoc --version
```



### linux安装

```bash
# 示例下载（替换为最新版本号）
wget https://github.com/protocolbuffers/protobuf/releases/download/v28.0/protoc-28.0-linux-x86_64.zip
unzip protoc-28.0-linux-x86_64.zip -d protoc
# 将二进制复制到系统PATH
sudo cp protoc/bin/protoc /usr/local/bin/
# 把include里的proto标准库复制出来（重要！google/protobuf/*.proto）
sudo cp -r protoc/include/google /usr/local/include/

# 验证安装
protoc --version
```





---



## laravel使用grpc

**安装依赖**

```php
composer require grpc/grpc
composer require google/protobuf
```



