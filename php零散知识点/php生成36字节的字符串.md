[^author]:姚留洋
[^created_at]:3032/11/22

# 说明

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

