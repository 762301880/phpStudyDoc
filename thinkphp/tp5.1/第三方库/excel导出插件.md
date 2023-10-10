# [easy-excel](https://github.com/jqhph/easy-excel)

**说明**

> `Easy Excel`是一个基于 [box/spout](https://github.com/box/spout) 封装的Excel读写工具，可以帮助开发者更快速更轻松地读写Excel文件， 并且无论读取多大的文件只需占用极少的内存。

**资料**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 官方文档 | [link](https://jqhph.github.io/easy-excel/docs/master/export.html) |

##  安装与使用

### **安装**

> ### 环境
>
> `PHP` >= 7.1
> `PHP` extension `php_zip`
> `PHP` extension `php_xmlreader`
> `box/spout` >= 3.0
> `league/flysystem` >= 1.0

```php
composer require dcat/easy-excel
```

### [导出](https://jqhph.github.io/easy-excel/docs/master/export.html)

> 更多的示例查看文档就行这里只给一个简单得案例

```php
 $data=Stu::all();
        // 设置标题，并更改列的先后顺序，位置可以随意更改
        $headings=[
           'sname'=> '名称',
           'sex'=> '性别',
           'birthday'=> '生日',
        ];
        return Excel::export($data)->headings($headings)->download('users.xlsx');
```

