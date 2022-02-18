# 资料

| 名称                   | 地址                                                       |
| ---------------------- | ---------------------------------------------------------- |
| thinkphp6.0-多应用模式 | [link](https://www.kancloud.cn/manual/thinkphp6_0/1297876) |
|                        |                                                            |

# 说明

```php
# 安装后默认使用单应用模式部署，目录结构如下：

├─app 应用目录
│  ├─controller         控制器目录
│  ├─model              模型目录
│  ├─view               视图目录
│  └─ ...               更多类库目录
│
├─public                WEB目录（对外访问目录）
│  ├─index.php          入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于apache的重写
│
├─view                  视图目录
├─config                应用配置目录
├─route                 路由定义目录
├─runtime               应用的运行时目录
# 单应用模式的优势是简单灵活，URL地址完全通过路由可控。配合路由分组功能可以实现类似多应用的灵活机制    
```



> 开发的时候我们会把所有的控制器和控制器放在一起,
>
> 视图层与视图层放在一起,集中起来进行开发,当项目很大
>
> 的时候我们需要对应的一个的去寻找依赖,很不便于维护,而多应用
>
> 模式看起来花里胡哨其实就是对我们所开发的功能模块进行
>
> 细分，例如我们开发一个配送模块这一个相关模块中，有视图，
>
> 控制器，模型等我们放置在一起为这个功能提供专属的业务服务
>
> Tp6说明
>
> 框架需要用到多应用模块的时候，并不能像tp5那样直接创建一个模块即可，需要composer安装对应的think-multi-app扩展才能够使用。

## 安装多应用模块扩展：think-multi-app：

- 使用composer 安装

```php
composer require topthink/think-multi-app
```

# 使用

> 使用多应用模块的时候需***要将*****app**目录下的***controller***目录**删除**，然后执行如下指令，生成index模块：

## 生成多应用模块

```php
php think build 你的模块名 
```

- 生成的模块示例

  > 在app目录下

![2021-08-14 21-51-55屏幕截图.png](https://i.loli.net/2021/08/14/fTCMhoRBd2LpwOg.png)

## 下载安装think-view模板引擎驱动扩展：

- 使用composer 安装

```shell
composer  require topthink/think-view
```



## 多应用模式下的路由使用问题

需要在单个模块下建立***route\app.php***文件

```shell
<?php
use think\facade\Route;

Route::get('test',function (){
    return 'Hello World';
});
Route::get('test','你的控制器名/你需要访问的方法名');
# 访问路由
你的虚拟域名/admin/test  # admin 是模块名 /test 是路由名
```



# 多应用模式`api`使用实战

## 创建模块

**说明**

> 例如我们需要创建一个**demo**模块

```php
 php think build demo  # c
```

