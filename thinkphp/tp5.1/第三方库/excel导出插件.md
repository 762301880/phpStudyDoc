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

# **[XLSXWriter](https://github.com/mk-j/PHP_XLSXWriter)**(不推荐使用 只是做记录 太老的东西了)

## 资料

| 名称     | 地址                                                |
| -------- | --------------------------------------------------- |
| github   | [link](https://github.com/mk-j/PHP_XLSXWriter)      |
| 网盘备份 | [link](https://yaoliuyang.lanzoul.com/ixDnS2dpy18j) |

**使用**

> 克隆仓库之后直接复制**[xlsxwriter.class.php](https://github.com/mk-j/PHP_XLSXWriter/blob/master/xlsxwriter.class.php)**

```php
<?php
namespace app\lib;
/*
 * @license MIT License
 * */
use ZipArchive;
class XLSXWriter
{
    //...... 忽略复制代码
}
```

## 简单使用

> 50000 rows: (1.4s, 0MB memory usage)

```php
use app\lib\XLSXWriter;

public function test(){
    $writer = new XLSXWriter();
    $writer->writeSheetHeader('Sheet1', array('c1'=>'integer','c2'=>'integer','c3'=>'integer','c4'=>'integer') );
    for($i=0; $i<50000; $i++)
    {
        $writer->writeSheetRow('Sheet1', array($i, $i+1, $i+2, $i+3) );
    }
    $writer->writeToFile('huge.xlsx');
    echo '#'.floor((memory_get_peak_usage())/1024/1024)."MB"."\n";
}
```

