# 资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| laravel-Sail-文档手册 | [link](https://laravel.com/docs/8.x) [link](https://laravel.com/docs/8.x/sail) |

# 使用

> 你可以使用一个简单的终端命令来创建一个新的 Laravel 项目。例如，要在名为“example-app”的目录中创建一个新的 Laravel 应用程序，您可以在终端中运行以下命令：
>
> 此项目会保存在本机目录

```shell
curl -s "https://laravel.build/项目名称" | bash

# 例子
[root@VM-16-5-centos ~]# curl -s "https://laravel.build/laravel_test" | bash
Unable to find image 'laravelsail/php81-composer:latest' locally
Trying to pull repository docker.io/laravelsail/php81-composer ... 
latest: Pulling from docker.io/laravelsail/php81-composer
eff15d958d66: Pull complete 
933427dc39f7: Pull complete 
35bb08dc7ee2: Pull complete 
58a3f26800d7: Pull complete 
28803bfad74c: Pull complete 
341a482d0bd3: Pull complete 
03c4148389b1: Pull complete 
fa3a9bfc7793: Pull complete 
024907232600: Pull complete 
df5987d70de6: Pull complete 
e2a72a518bd8: Pull complete 
b1ba689993c8: Pull complete 
3bd142ab0b85: Pull complete 
872fe34989f0: Pull complete 
Digest: sha256:b27920b769ad8dc036a9ede3ae36f51a280d370ec7d125e77ca1924c9fa21dbb
Status: Downloaded newer image for docker.io/laravelsail/php81-composer:latest

 _                               _
| |                             | |
| |     __ _ _ __ __ ___   _____| |
| |    / _` | '__/ _` \ \ / / _ \ |
| |___| (_| | | | (_| |\ V /  __/ |
|______\__,_|_|  \__,_| \_/ \___|_|

Warning: TTY mode requires /dev/tty to be read/writable
。。。。。。。。。。。。

Get started with: cd laravel_test && ./vendor/bin/sail up  # 出现这句代表安装完成

#进入项目
cd laravel_test
# 运行sailup-记着一定要安装docker-composer，运行sailup 可以启用docker 并生成一个隔离的运行环境比如安装mysql啊等d
./vendor/bin/sail up


```

