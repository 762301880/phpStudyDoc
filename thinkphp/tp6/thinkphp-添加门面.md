# 说明&资料



## 资料

| name                    | url                                                        |
| ----------------------- | ---------------------------------------------------------- |
| thinkphp文档-门面的使用 | [link](https://www.kancloud.cn/manual/thinkphp6_0/1037491) |
|                         |                                                            |



# 示例

## 创建门面类

```php
<?php

namespace app\admin\Facades;

use app\Utils\Printer\PrinterService;
use think\Facade;

class Printer extends Facade
{
    protected static function getFacadeClass()
    {
       // return 'app\Utils\Printer\PrinterService'; # 指定门面方法的工具类
        return PrinterService::class; # 指定门面方法的工具类
    }
}
```

> thinkphp 指定门面不像laravel 那么复杂只需要创建门面类然后指定工具类即可，工具类自己创建

## 使用

```shell
use app\admin\Facades\Printer;
Printer::指定工具类下面的方法();
```

