## 资料

[官方文档](https://www.php.net/manual/zh/language.oop5.interfaces.php)

## 什么是接口

**接口（interface）** 理解成：

**一份 “必须遵守的规则清单”**

只规定**要实现哪些方法**，不写具体代码；

谁实现这个接口，**谁就必须把这些方法写完整**。

##  定义接口的语法

```php
interface 接口名 {
    // 只能定义：常量 + 方法声明（没有方法体）
    public function 方法名1();
    public function 方法名2($参数);

```



## 定义接口规则

接口用 `interface` 定义，**不是 class**

方法**只有声明，没有 body**（没有 `{}` 里的代码）

所有方法默认都是 `public`，**不能写 private/protected**

## 





## 完整示例（一看就懂）

### ① 定义一个接口

```php
// 定义接口：用户服务接口
interface UserServiceInterface
{
    // 规定：必须实现获取用户信息的方法
    public function getUserInfo($id);

    // 规定：必须实现修改用户名的方法
    public function updateUserName($id, $name);
}
```

### ② 类去 “实现” 接口

使用 `implements`

```php
// 类实现接口 → 必须把接口里的方法全部写出来
class UserService implements UserServiceInterface
{
    // 必须实现！不写就报错
    public function getUserInfo($id)
    {
        return ['id' => $id, 'name' => '张三'];
    }

    // 必须实现！
    public function updateUserName($id, $name)
    {
        return true;
    }
}
```

### ③ 如果不实现接口方法 → 直接报错

PHP 强制检查，**少一个方法都不行**。

------

## 4. 接口能干嘛？（实际用途）

### ① 统一规范（多人协作必备）

大家都按同一个规则写代码，不会乱。

### ② 依赖注入、解耦（Hyperf / Laravel 常用）

```php
// 不依赖具体类，依赖接口 → 代码更灵活
function test(UserServiceInterface $userService)
{
    $userService->getUserInfo(1);
}
```

### ③ 一个类可以实现**多个接口**

```php
class A implements Interface1, Interface2, Interface3
{
}
```

------

## 5. 接口 vs 抽象类（简单区分）

- **接口**：只有规则，没有任何实现
- **抽象类**：可以有规则 + 可以有部分实现

------

### 总结

1. **接口 = 规则清单**
2. 用 `interface` 定义
3. 用 `implements` 实现
4. 实现类**必须写完所有方法**
5. 所有方法都是 `public`

# 注意事项

如果定义接口类文件夹 **文件夹定义为 Interfaces**   因为Interface 是 PHP 的关键字，在 PHP 8.0 之前不能用作命名空间的一部分。



