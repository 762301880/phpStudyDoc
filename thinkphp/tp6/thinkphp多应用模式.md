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
 php think build demo  # 创建生成多应用demo模块
# 生成大概以下目录     
├─app 应用目录
│  ├─demo              后台应用
│  │  ├─controller      控制器目录
│  │  ├─model           模型目录
│  │  ├─view            视图目录
│  │  ├─config          配置目录 # 此目录需要自己创建
│  │  ├─route           路由目录 # 此目录需要自己创建 路由文件可以随意定义 例如 demo.php
│  │  └─ ...            更多类库目录
│
├─public                WEB目录（对外访问目录）
│  ├─admin.php          后台入口文件
│  ├─index.php          入口文件
│  ├─router.php         快速测试文件
│  └─.htaccess          用于apache的重写
│
├─config                全局应用配置目录
├─runtime               运行时目录
│  ├─index              index应用运行时目录
│  └─admin              admin应用运行时目录     
```

**大概生成的目录如下**

> 默认里面只有一个**Index**控制器文件下面有一个**index**方法

![image-20220218102957568](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220218102957568.png)

**访问demo模块下面的index方法**

> 我自己配置的虚拟域名是**www.thinkphp.com**, 

> 如果访问报错
>
> Not Found
>
> The requested URL /demo/index/index was not found on this server.
>
> Additionally, a 404 Not Found error was encountered while trying to use an ErrorDocument to handle the request.
>
> 那是因为thinkphp默认暴露**index.php**你需要隐藏[参考资料](https://www.cnblogs.com/yaoliuyang/p/12410193.html)。或者直接**你的域名/index.php/demo/index控制器/index方法**访问
>
> ```shell
> # 在public/.htaccess中配置添加
> <IfModule mod_rewrite.c>
> Options +FollowSymlinks -Multiviews
> RewriteEngine On
> RewriteCond %{REQUEST_FILENAME} !-d
> RewriteCond %{REQUEST_FILENAME} !-f
> RewriteRule ^(.*)$ index.php?/$1 [QSA,PT,L] # 核心就是在 index.php后加一个?隐藏的意思
> </IfModule>
> ```

![image-20220218103136085](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220218103136085.png)

**自定义控制器&方法**

```php
# 如上上图所示我们自定义Demo控制器类
<?php


namespace app\demo\controller;


use app\BaseController;

class Demo extends BaseController
{
    public function aaa()
    {
        return '我是demo控制器下面的aaa方法';
    }
}
# 访问 http://www.thinkphp.com/demo/demo/aaa

# 结果返回
我是demo控制器下面的aaa方法
```

**路由访问**

![image-20220218103855313](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220218103855313.png)

> 自定义**route**文件夹 这里只设置一个闭包用于测试,直接访问**demo**模块下的**demo_test**路由即可访问

![image-20220218103952781](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220218103952781.png)



**路由指向控制器**

```shell
<?php

use think\facade\Route;

Route::any('demo_test','Demo/aaa'); # 设置路由名称为demo_test   访问Demo控制器下面的aaaf

# 直接访问
http://www.thinkphp.com/demo/demo_test

# 结果返回
我是demo控制器下面的aaa方法
```





# bug解析

## 初次使用多应用模式出现<font color='red'>No input file specified.</font>

[**参考资料**](https://blog.csdn.net/weixin_41965172/article/details/104369354) 

**解决方案**

```shell

# 你的项目\public\.htaccess

<IfModule mod_rewrite.c>
  Options +FollowSymlinks -Multiviews
  RewriteEngine On

  RewriteCond %{REQUEST_FILENAME} !-d
  RewriteCond %{REQUEST_FILENAME} !-f
  #   RewriteRule ^(.*)$ index.php/$1 [QSA,PT,L]  修改为   RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
  RewriteRule ^(.*)$ index.php [L,E=PATH_INFO:$1]
</IfModule>
```

