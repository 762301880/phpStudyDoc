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

#### protobuf 扩展（忽略这个,未看到dll版本）

同样选：php7.4 NTS x64，推荐版本 **3.21.12**（PHP7.4 兼容稳定版）PECL 下载：https://pecl.php.net/package/protobuf

- `php_protobuf.dll` → ext 目录
- php.ini 增加：

```ini
extension=protobuf
```