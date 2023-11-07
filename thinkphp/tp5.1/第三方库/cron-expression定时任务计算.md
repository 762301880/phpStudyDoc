#  [dragonmantank/](https://packagist.p2hp.com/packages/dragonmantank/)cron-expression

**资料**

| 名称                 | 地址                                                         |
| -------------------- | ------------------------------------------------------------ |
| packagist官网地址    | [link](https://packagist.p2hp.com/packages/dragonmantank/cron-expression) |
| 菜鸟教程-crontab介绍 | [link](https://www.runoob.com/linux/linux-comm-crontab.html) |

## 使用

**安装**

```php
composer require dragonmantank/cron-expression
```

**简单使用示例**

```php
        $cron=new CronExpression("* * * * *"); //可以写上自己需要判断的时间节点
        //确定cron是根据当前日期还是特定日期运行。此方法假定当前的秒数是不相关的，并且应该每分钟调用一次。
        $cron->isDue();
        //getNextRunDate 获取相对于当前日期或特定日期的下一个运行日期
        echo $cron->getNextRunDate()->format('Y-m-d H:i:s');  //输出下一分钟
```

