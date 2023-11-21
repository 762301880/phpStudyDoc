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

## php生成36字节的字符串.md

说明

> 最近又整合了一套打印机api,其中有一个生成36字节的字符串功能，采用**laravel**自带的**Str::random(32)**
>
> 生成的32位字符用不了，还好打印机提供的sdk中有这个生成的函数找到了拿过来直接用

**打印机生成36字节的字符串文档地址**

```shell
http://doc2.10ss.net/332000
# sdk使用说明
http://doc2.10ss.net/337744
```



**代码示例**

```shell
  public function uuid4()
    {
        mt_srand((double)microtime() * 10000);
        $charid = strtolower(md5(uniqid(rand(), true)));
        $hyphen = '-';
        $uuidV4 =
            substr($charid, 0, 8) . $hyphen .
            substr($charid, 8, 4) . $hyphen .
            substr($charid, 12, 4) . $hyphen .
            substr($charid, 16, 4) . $hyphen .
            substr($charid, 20, 12);
        return $uuidV4;
    }
```

## 铭感词库过滤(待补充)

**资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| 铭感词sql下载    | [link](https://download.csdn.net/download/Helenzhn/85235189?spm=1003.2122.3001.6634.1) |
| 个人备份铭感词库 | [link](https://yaoliuyang.lanzoul.com/izMre182vl6f)          |





























