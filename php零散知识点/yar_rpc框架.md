# 资料

| 名称                        | 地址                                                         |
| --------------------------- | ------------------------------------------------------------ |
| php-yar官方文档，github说明 | [link](https://www.php.net/manual/zh/book.yar.php) [link](https://github.com/laruence/yar) |
| 第三方博客                  | [link](https://www.laruence.com/2012/09/15/2779.html)  [link](https://blog.csdn.net/gaitiangai/article/details/105821340) [link](https://www.kancloud.cn/a173512/php_note/1690781) [link](http://www.9ong.com/032021/php%E5%87%BD%E6%95%B0%E5%8F%82%E8%80%8333-%E4%B8%80%E6%AC%A1%E6%B8%85%E6%A5%9Arpc%E6%A1%86%E6%9E%B6yar.html#github%E5%AE%98%E6%96%B9%E5%9C%B0%E5%9D%80%E5%8F%8A%E9%B8%9F%E5%93%A5%E5%85%B3%E4%BA%8Eyar%E7%9A%84%E6%96%87%E7%AB%A0) [link](https://www.codercto.com/a/69081.html) |

**说明**

> RPC是远程过程调用（Remote Procedure Call）的缩写形式,**Yar**是一个轻量级的并行的[RPC](https://baike.baidu.com/item/%E8%BF%9C%E7%A8%8B%E8%BF%87%E7%A8%8B%E8%B0%83%E7%94%A8/7854346?fromtitle=RPC&fromid=609861&fr=aladdin)框架，也是一个PHP扩展，
>
> **php-yar介绍**
>
> Yar 是一个轻量级, 高效的RPC框架, 它提供了一种简单方法来让PHP项目之间可以互相远程调用对方的本地方法. 并且Yar也提供了并行调用的能力. 可以支持同时调用多个远程服务的方法.
>
> **个人理解**
>
> 内部服务相互调用尽量使用**rpc** 例如 本地服务项目1调用本地服务项目2中的方法

# 安装

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.cxyzjd.com/article/qq_40848163/109119518) |



## windows 安装

[**下载yar扩展**](https://pecl.php.net/package/yar)

> 请在[**pecl扩展官网下载**](https://pecl.php.net/package/yar),对应**php(不知道版本可以终端php -v查看)**版本

  **点击DLL**下载windows扩展

<img src="https://s2.loli.net/2022/05/13/cBW3qCeNXJitwsS.png" alt="1652404037(1).jpg" style="zoom:50%;" />

**下载对应版本**

>  Thread Safe([线程安全](https://so.csdn.net/so/search?q=线程安全&spm=1001.2101.3001.7020))和 None Thread Safe(NTS，非线程安全)  本人下载的是**None Thread Safe**,**是否线程安全可以打印phpinfo()**
>
> 查询线程是否安全

![1652404126(1).jpg](https://s2.loli.net/2022/05/13/T1nKjFztwLaS8Gy.png)

> 线程安全:禁用

![1652404349(1).jpg](https://s2.loli.net/2022/05/13/dXYoGuwZnV42CSt.png)

下载完成之后解压

> 将**php_yar.dll**文件复制到，**php**项目文件的**ext**扩展包中,由于我使用的是**phpstudy集成环境**所以目录是**D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext**
>
> 然后编辑**php.ini**配置文件中添加**extension=php_yar.dll** 然后重启php&&apache即可

```php
# 查询是否安装成功
C:\Users >php -m
[PHP Modules]
****
yar
****
[Zend Modules]
# or php --ri yar
C:\Users >php --ri yar

yar
    
yar support => enabled
Version => 2.2.1

Directive => Local Value => Master Value
yar.packager => php => php
yar.transport => curl => curl
yar.debug => Off => Off
yar.expose_info => On => On
yar.connect_timeout => 1000 => 1000
yar.timeout => 5000 => 5000
yar.content_type => application/octet-stream => application/octet-stream    
```

## linux安装

### [下载软件包](https://pecl.php.net/package/yar)

```shell
# 下载软件包
wget https://pecl.php.net/get/yar-2.3.2.tgz && tar -zxvf yar-2.3.2.tgz && cd yar-2.3.2
```



### alpine 安装

```shell
apk add php-devel  # 安装phpize
# 执行编译
bash-5.0# phpize7
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902

# 如果报错
Cannot find autoconf. Please check your autoconf installation and the
$PHP_AUTOCONF environment variable. Then, rerun this script.
# 安装 autoconf
bash-5.0# apk add autoconf
(1/6) Installing m4 (1.4.18-r1)
(2/6) Installing perl (5.30.3-r0)
....

# 继续执行phpize7 出现以下没有报错则代表编译成功
bash-5.0# phpize7
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902

bash-5.0# ./configure 
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for pkg-config... no
checking for cc... no
checking for gcc... no
configure: error: in `/root/yar-2.3.2':
configure: error: no acceptable C compiler found in $PATH
See `config.log' for more details

# 上一步编译解决方案(没有安装编译软件)
bash-5.0# apk add gcc 
bash-5.0# apk add g++

# 再次编译
bash-5.0# ./configure 
checking for grep that handles long lines and -e... /bin/grep
checking for egrep... /bin/grep -E
checking for a sed that does not truncate output... /bin/sed
checking for pkg-config... no
checking for cc... cc
checking whether the C compiler works... yes
checking for C compiler default output file name... a.out
checking for suffix of executables... 
checking whether we are cross compiling... no
checking for suffix of object files... o
checking whether we are using the GNU C compiler... yes
checking whether cc accepts -g... yes
checking for cc option to accept ISO C89... none needed
checking how to run the C preprocessor... cc -E
checking for icc... no
checking for suncc... no
checking for system library directory... lib
checking if compiler supports -R... no
checking if compiler supports -Wl,-rpath,... yes
checking build system type... x86_64-pc-linux-musl
checking host system type... x86_64-pc-linux-musl
checking target system type... x86_64-pc-linux-musl
configure: error: Cannot find php-config. Please use --with-php-config=PATH  # 说找不到php-config 让我们指定php配置文件未知
# 安装php-config
apk add php-dev
# 查询php-config7
bash-5.0# find / -name php-co*
/usr/bin/php-config7
# 继续执行编译
bash-5.0# ./configure --with-php-config=/usr/bin/php-config7
# 如果报错
configure: error: Please reinstall the libcurl distribution - easy.h should be in <curl-dir>/include/curl/
bash-5.0# apk add curl-dev
# 继续执行编译
bash-5.0# ./configure --with-php-config=/usr/bin/php-config7

# 如果报错  https://blog.csdn.net/ligaofeng/article/details/105879032?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-105879032-blog-84377911.pc_relevant_recovery_v2&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-105879032-blog-84377911.pc_relevant_recovery_v2&utm_relevant_index=2
creating libtool
appending configuration tag "CXX" to libtool
configure: patching config.h.in
configure: creating ./config.status
config.status: creating config.h
config.status: config.h is unchanged
# 安装 libtool ps 没啥用
#bash-5.0# apk add libtool

# 执行安装
bash-5.0# make && make install

# 如果报错
bash: make: command not found
# 安装
apk add make
# 出现以下则代表安装成功
more information, such as the ld(1) and ld.so(8) manual pages.
----------------------------------------------------------------------

Build complete.
Don't forget to run 'make test'.

Installing shared extensions:     /usr/lib/php7/modules/       # 里面有  yar.so

# 添加扩展到php配置文件 
bash-5.0# find / -name php.ini
/etc/php7/php.ini

```



# 项目中使用实例

```php
# 项目A-服务端

class API
{
    /**
     * the doc info will be generated automatically into service info page.
     * @params
     * @return
     */
    public function test($name)
    {
        return $name;

    }

    public function test2()
    {
        return 'test2';
    }

}
$service = new \Yar_Server(new API());
$service->handle();
# 项目B-客户端(调用输出 张三)
/**
 * 串行化调用
 */
$client = new \Yar_Client("http://www.cs.com/phpinfo.php");
return $client->test('张三');
/**
 * 并行的服务调用
 */
$url = "http://www.cs.com/phpinfo.php";
\Yar_Concurrent_Client::call("$url", "test", array("parameters"), [$this,'callBack'] );
\Yar_Concurrent_Client::call("$url", "test", array("parameters"), [$this,'callBack']);
\Yar_Concurrent_Client::call("$url", "test", array("parameters"), [$this,'callBack']);
\Yar_Concurrent_Client::call("$url", "test", array("parameters"), [$this,'callBack']);
\Yar_Concurrent_Client::loop(); //send

public function callBack($retval, $callinfo)
 {
   var_dump($retval);
 }
```

**laravel项目中使用实例**

```php
# 1. 封装servers服务层调用父类 自动注册当前服务类,提供给外部调用的方法不要写在控制器层面,写在server中
class YarService 
{
    public function __construct()
    {
        //判断扩展是否存在
        //实例化Yar_Server
        $server = new \Yar_Server($this);
        // 启动server
        $server->handle();
    }
} 
# 关于 yar_client 返回数据打印 可以用 printer  var_dump 等函数打印返回空白的数据
return $client->test('张三');
```



## 补充

**判断扩展是否存在**

```php
extension_loaded('yar');  # 判断yar扩展是否存在返回 bool类型
```



# phpstorm对应扩展提示设置

**说明**

> 如果我们安装完成扩展后但是些项目种并没有代码提示(很不幸本人就是离开了提示活不下去的那种),请跟随我设置
>
> 点击**phpstorm**左上角导航栏 **File-Settings** 找到**Languages&Framework -PHP** 先选择对应的语言等级然后再找到下面的
>
> **PHP Runtime 勾选扩展**点击**确定回到编辑器既可以有提示**

![1652405064(1).png](https://s2.loli.net/2022/05/13/3CanPeRZ7sbpvhY.png)