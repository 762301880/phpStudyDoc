# compose 安装

## centos

> [官方安装文档](https://getcomposer.org/doc/00-intro.md#installation-linux-unix-macos)

### 1.打开composer[官网](https://getcomposer.org/)

<a href='https://getcomposer.org/'><img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518151546172-487261862.png' heigth='400px' width='600px' title='点击图片跳转'></a>

### 2.右键复制

<img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518151818063-2008282308.png' heigth='400px' width='600px' title='示例'>

### 3.`wget`下载安装软件：

#### 3.1下载

>wget是一个下载文件的工具。对于Linux用户是必不可少的工具
```shell
wget https://getcomposer.org/download/2.0.2/composer.phar
```

<img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518152634593-1774510773.png' heigth='400px' width='600px' title='示例'>

#### 3.2安装

```shell
mv composer.phar /usr/local/bin/composer #移动composer.phar到新的目录
cd /usr/local/bin/ #进入到composer新的目录
chmod -R 777 /usr/local/bin/composer 修改权限，否则执行会出错
# 一行命令执行上面
mv composer.phar /usr/local/bin/composer && cd /usr/local/bin/ && chmod -R 777 /usr/local/bin/composer
```
>`composer`切换[镜像源](https://www.cnblogs.com/yaoliuyang/p/12815601.html)

### 安装之后的说明

>以上就已经安装好的，有一个问题就是composer是依赖于php环境的所以
>如果你的linux系统中安装了php环境就可以直接使用composer了

#  compose 切换镜像源

## 1.说明
> 在使用composer 的时候一直感觉很慢,原因是composer使用的是国外的镜像，
> 下载软件慢到可能被墙，所以我们需要切换使用国内的镜像源

## 2.资料
| 参考示例                                        | 镜像大全                                                     | laravel-china-composer                                       |
| ----------------------------------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| [aliyun](https://developer.aliyun.com/composer) | [腾讯云](https://cloud.tencent.com/developer/article/1550787) | [laravel-china-composer](https://learnku.com/php/t/4484/composer-mirror-use-help) |

**这条命令查看你的镜像用的是那个地方的资源**

```shell
composer config -g -l repo.packagist
```

<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200502010848688-470662516.png' heigth='400px' width='600px' title='查看镜像源'>



## 3.切换镜像源

>阿里云的cmoposer镜像

```shell
composer config -g repo.packagist composer https://mirrors.aliyun.com/composer/
```

#  补充

##  compose更新

```shell
# 更新composer 软件到最新版本
composer self-update
```

#  疑问解答

## Composer install 与 update 的区别

**composer install**

> 作用：根据 composer.lock 文件中记录的版本信息安装依赖。
> 场景：适用于首次安装项目依赖或在不同环境中复现相同的依赖环境。
> 特点：不会改变 composer.lock 文件中的版本信息。



**composer update**

> 作用：根据 composer.json 文件中的版本约束更新所有依赖到最新版本，并更新 composer.lock 文件。
> 场景：适用于开发过程中需要获取最新依赖版本的情况。
> 特点：会修改 composer.lock 文件中的版本信息，确保下次 composer install 时安装的是最新的依赖版本。



**总结**
composer install：安装锁定的依赖版本，适合生产环境。
composer update：更新依赖到最新版本，适合开发环境(开发环境万不可用)。

##  php 原生扩展 与composer 包的区别

### 定义和用途

**PHP 原生扩展**：

- 定义：PHP 原生扩展是用 C 语言编写的动态链接库（.so 文件在 Linux 上，.dll 文件在 Windows 上），它们可以直接被 PHP 解释器加载和使用。
- 用途：原生扩展通常用于实现高性能的功能，如数据库连接、图像处理、加密解密等。这些功能在纯 PHP 代码中难以高效实现。

**Composer 包**：

- 定义：Composer 是 PHP 的依赖管理工具，它允许开发者通过 composer.json 文件声明项目所需的第三方库（包）。这些包通常是用 PHP 编写的类库或框架。
- 用途：Composer 包主要用于管理和安装项目的依赖，简化开发和维护过程。常见的包包括 ORM 框架、日志库、缓存库等。

### 安装和使用

**PHP 原生扩展**:

- 安装：通常需要编译安装，可以通过 PECL（PHP Extension Community Library）来安装。例如，安装 mysqli 扩展

  ```shell
  pecl install mysqli
  ```

- 使用：在 php.ini 文件中启用扩展，例如

  ```shell
   extension=mysqli.so
  ```

**Composer 包**

- 安装：通过 Composer 命令安装，例如，安装 monolog 日志库：

  ```shell
  composer require monolog/monolog
  ```

- 使用：在项目中通过 require 或 use 语句引入并使用包中的类或函数。例如：

  ```shell
      use Monolog\Logger;
      use Monolog\Handler\StreamHandler;
  
      $logger = new Logger('name');
      $logger->pushHandler(new StreamHandler(__DIR__ . '/app.log', Logger::WARNING));
      $logger->warning('This is a warning');
  ```

###  性能和灵活性

**PHP 原生扩展：**

- 性能：由于是用 C 语言编写，原生扩展通常具有更高的性能，尤其是在处理大量数据或复杂计算时。
- 灵活性：相对较低，因为需要编译和配置，且对操作系统和 PHP 版本有依赖。

**Composer 包：**

- 性能：性能一般，但足以满足大多数应用的需求。对于性能要求极高的场景，可以考虑使用原生扩展。
- 灵活性：非常高，因为包是用 PHP 编写的，易于安装和卸载，且不依赖于特定的操作系统或 PHP 版本。

### 社区和支持

**PHP 原生扩展：**

- 社区：相对较小，主要由核心开发者和高级用户维护。
- 支持：文档和资源相对较少，遇到问题时可能需要深入研究源代码或寻求社区帮助。

**Composer 包:**

- 社区：非常活跃，有大量的开发者和贡献者。
- 支持：文档丰富，社区支持强大，遇到问题时容易找到解决方案

###  适用场景

**PHP 原生扩展：**

- 适用于需要高性能、低延迟的应用，如高性能数据库驱动、图像处理库等。
- 适用于需要直接访问底层系统资源的场景。

**omposer 包：**

- 适用于大多数 Web 开发场景，如 MVC 框架、ORM、日志库、缓存库等。
- 适用于快速开发和迭代的项目。

### 总结

PHP 原生扩展和 Composer 包各有优缺点，选择哪种方式取决于具体的应用需求。原生扩展适合高性能和低延迟的场景，而 Composer 包则更适合快速开发和维护。