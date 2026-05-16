#  一、说明

[`__invoke`魔术方法官方说明](https://www.php.net/manual/zh/language.oop5.magic.php#object.invoke)

>有时候我们需要一个控制器只有一个方法,这个时候就可以用`php`的`__invoke`，魔术方法，此方法
>
>调用函数的方式调用一个对象时，[__invoke()](https://www.php.net/manual/zh/language.oop5.magic.php#object.invoke) 方法会被自动调用。

# 二、使用示例

> 如果调用 调用微信

## 1.路由创建

```php
Route::post('/auth/wechat/login', 'Api\Xcx\AuthWechatLoginController');//微信授权登录,不需要指定方法
```

## 2.控制器创建

```php
public function __invoke()
 {
     #业务逻辑
 }
```

## 接着你就可以直接写业务逻辑了

