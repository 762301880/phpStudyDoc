# 说明

> 枚举(enum)，开发过程中我们会遇到这种情况比如我们需要查询类型为已完成的数据，假设数据库
>
> type字段为1已完成 0未完成 ，写代码的时候很多业务逻辑都需要指定查询类型为1或者0 的代码
>
> 如果将数值类型1或者0写在很多代码逻辑中很不便于维护，假设以后我们需要对业务逻辑进行更改
>
> 更改为2为已完成，难道还需要一个一个的找代码中查询约束为1 的逻辑吗，这个使用就需要使用
>
> 枚举类型





# 代码示例

> 定义一个空文件叫enums,可以创建自己需要的业务枚举文件 业务名称+enum.php

其中的代码示例

```php
class PrinterEnums
{
    /**
     * 打印机对应顺序配置
     * 此配置需要在config/printer中配置对应的打印机配置
     */
    const JIA_BO_YUN = 1;//佳博云打印机
    const TEST_PRINTER = 2;//测试打印机
    //d 
    public static function map(): array
    {
        return [
            //映射上面的变量
            self::JIA_BO_YUN => 'jia_bo_yun',
            self::TEST_PRINTER => 'test_printer',//测试打印机
        ];
    }
}
```

