# compose 安装

## centos

### 1.打开composer[官网](https://getcomposer.org/)

<a href='https://getcomposer.org/'><img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518151546172-487261862.png' heigth='400px' width='600px' title='点击图片跳转'></a>

### 2.右键复制

<img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518151818063-2008282308.png' heigth='400px' width='600px' title='示例'>

### 3.`wget`下载安装软件：

#### 3.1下载

>wget是一个下载文件的工具。对于Linux用户是必不可少的工具
```
wget https://getcomposer.org/download/2.0.2/composer.phar
```

<img src='https://img2020.cnblogs.com/blog/1922055/202005/1922055-20200518152634593-1774510773.png' heigth='400px' width='600px' title='示例'>

#### 3.2安装

```
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

```php
composer config -g -l repo.packagist
```

<img src='https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1922055-20200502010848688-470662516.png' heigth='400px' width='600px' title='查看镜像源'>



## 3.切换镜像源

>阿里云的cmoposer镜像

```php
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

```shell
作用：根据 composer.lock 文件中记录的版本信息安装依赖。
场景：适用于首次安装项目依赖或在不同环境中复现相同的依赖环境。
特点：不会改变 composer.lock 文件中的版本信息。
```



**composer update**

```shell
作用：根据 composer.json 文件中的版本约束更新所有依赖到最新版本，并更新 composer.lock 文件。
场景：适用于开发过程中需要获取最新依赖版本的情况。
特点：会修改 composer.lock 文件中的版本信息，确保下次 composer install 时安装的是最新的依赖版本。
```



**总结**
composer install：安装锁定的依赖版本，适合生产环境。
composer update：更新依赖到最新版本，适合开发环境。