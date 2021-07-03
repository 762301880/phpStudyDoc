# 一、资料

| 资料名称                               | 资料地址                                                 |
| -------------------------------------- | -------------------------------------------------------- |
| `laravel`学院`Swoole` 从入门到实战教程 | [地址](https://laravelacademy.org/books/swoole-tutorial) |
|                                        |                                                          |

# 二、`swoole`安装

## 2.1`Ubuntu安装swoole`

- 参考[资料](https://laravelacademy.org/post/9780)

## 如果是在服务器安装的话，以` Ubuntu` 系统为例，通过执行下列命令安装即可：

```shell
sudo pecl install swoole
```

- 如果提示 `Command 'pecl' not found, but can be installed with:`

```shell
sudo apt install php7.4-dev
sudo apt install php-pear
```

- 然后通过 `php -i | grep php.ini` 定位 `php.ini` 文件所在位置，并打开该配置文件，在文件末尾追加如下内容：

```shell
[swoole]
extension=swoole.so
```

- 保存并退出，在终端运行 `php -m`，如果看到扩展里包含 `swoole`，说明安装启用成功。