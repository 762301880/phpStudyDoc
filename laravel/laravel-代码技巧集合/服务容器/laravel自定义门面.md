# 资料

| name                       | url                                                          |
| -------------------------- | ------------------------------------------------------------ |
| 第三方博客                 | [link](https://ziruchu.com/art/23)                           |
| 第三方博客                 | [link](https://www.qianjinyike.com/laravel-%e6%a0%b8%e5%bf%83%e6%a6%82%e5%bf%b5%ef%bc%88%e6%9c%8d%e5%8a%a1%e5%ae%b9%e5%99%a8%e3%80%81%e6%9c%8d%e5%8a%a1%e6%8f%90%e4%be%9b%e8%80%85%e3%80%81facades%e3%80%81contracts%e3%80%81%e8%be%85/) |
| 服务容器上下文(第三方博客) | [link](https://segmentfault.com/q/1010000005945646)          |
| Laravel核心——Ioc服务容器   | [link](https://segmentfault.com/a/1190000009369477)          |



#  代码示例

##  定义工具类

> 此工具类中的方法主要可以定义门面后直接调用（此类可以在任意位置），或者指定代码中的任何类
>
> 例如以下我自定义了一个保存log的方法

![image-20210812102453851](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210812102453851.png)

## 定义门面

> 在项目中的app\Facades中定义，***类名称***可以自定义，需要继承`Facade`类，重写***getFacadeAccessor***
>
> 方法，返回的数据可以自定义但是需要与服务者中的绑定（一致）

![image-20210812102020349](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210812102020349.png)





## 定义服务提供者

> 在***register***方法中注册绑定，返回需要绑定的类(如果类中需要再次依赖类可以再次实例化类例如 return new LogTool(new "依赖的类");)

```php
# 使用artisan 命令定义服务提供者 此命令会在app\Providers中
php artisan  make:provider 自定义名称+ServiceProvider 

# 创建完成之后其中会有两个内置的方法  1 boot 监听程序,2 register 定义绑定关系 可以绑定自定义的服务类
```

![image-20210812102554909](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210812102554909.png)

# 注册服务提供者

在config/app.php文件中的providers属性中注册刚刚添加的服务提供者

```
//自定义注册服务提供者
App\Providers\SaveLogServiceProvider::class
//自定义门面
'SaveLog'=>App\Facades\SaveLog::class
```
