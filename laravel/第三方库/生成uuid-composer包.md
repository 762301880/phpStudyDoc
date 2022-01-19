# UUID说明

> 平时我们使用用户的关联信息什么的都是用的uid(mysql自增的id,例如:1,2,3,4)，假设我们抓包一个
>
> 接口 **http://www.article.com/234/article/13**  如果没有权限或者别有用心的人很容器就可以猜出
>
> 当前用户在用户表的位置安全隐患很大

## 资料

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| ramsey/uuid   官方文档 | [packagist ](https://packagist.org/packages/ramsey/uuid)    [官方文档](https://uuid.ramsey.dev/en/stable/introduction.html#what-is-a-uuid) |
| uuid-百度百科介绍      | [link](https://baike.baidu.com/item/UUID/5921266?fr=aladdin) |

> 既然有轮子我们就不需要自己造了

# 使用

## 安装

**composer安装**

```php
composer require ramsey/uuid
```

## 使用

> 安装 ramsey/uuid 后，启动并运行的最快方法是使用静态生成方法。

```php
use Ramsey\Uuid\Uuid;

$uuid = Uuid::uuid4();

printf(
    "UUID: %s\nVersion: %d\n",
    $uuid->toString(),    # 打印 8c586f08-4172-4a78-a8d8-8096d22bddc1    
    $uuid->getFields()->getVersion()
);
# 这将返回一个Ramsey\Uuid\Rfc4122\UuidV4.         8c586f08-4172-4a78-a8d8-8096d22bddc1    
```



**手动编写示例**

[参考资料](https://zhuanlan.zhihu.com/p/101958291)

```php
    public function random_origin_id()
    {
        mt_srand((double)microtime() * 10000);
        $charid = strtolower(md5(uniqid(rand(), true)));
        $uuidV4 =
            strtoupper(substr($charid, 0, 4)) . rand(1, 9) .
            substr($charid, 8, 1) . rand(1, 9) .
            substr($charid, 12, 4) . rand(1, 9) .
            substr($charid, 16, 4) . rand(1, 9) .
            substr($charid, 20, 12);
        return $uuidV4;
    }
```



