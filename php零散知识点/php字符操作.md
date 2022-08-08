# php 字符串操作

 

## 得到字符串的个数

> 举个栗子： 如果我们想得到 `我爱你`这三个字的个数

```php
mb_strlen() 函数
    使用
       $text="我爱你";
       $text=mb_strlen($text);
       var_dump($text);
       # 输出 3
```

## [复制字符的个数](http://c.biancheng.net/view/6146.html)

> 重复一个字符串 参数1 需要重复的字符，参数2 需要重复的个数

```php
str_repeat(0, 3);//自动填充0
# 输出 000
```

## 删除指定的字符

```shell
$value="/v2/order/list.html";
$newText=str_replace('.html','',$value);
# 输出
"/v2/order/list"
```

