## 说明

>今天写项目的时候因为一个bug烦恼了一天，因为thinkphp就是无法打印sql语句让我
>
>很是头痛看不见怎么执行语句

## 可以用buildSql语句直接把orm转化为原生sql

[官方文档](https://doc.thinkphp.cn/v5_1/query/zichaxun.html)

[备份文档](https://www.kancloud.cn/manual/thinkphp5_1/354032)

### 逻辑示例

> 下面给的示例不会写原生可以把sql构造为子查询原生sql

```php
$subQuery = Db::table('think_user')
    ->field('id,name')
    ->where('id', '>', 10)
    ->fetchSql(true)
    ->select();
# SELECT `id`,`name` FROM `think_user` WHERE `id` > 10 
```



## 对应使用的数据表

![2021-08-16 21-40-28屏幕截图.png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/x1XZvoEd3twWJHB.png)

![2021-08-16 21-40-07屏幕截图.png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/XHWsPh5Dynzrd8v.png)

```shell
 Stu::where('id',1)->LeftJoin('(SELECT cname , id AS c_id FROM class_s GROUP BY id ) AS class_s','stu.class_id = class_s.c_id',[])->select();
 
 # 等同于sql
 ( SELECT * FROM `stu` LEFT JOIN (SELECT cname , id AS c_id FROM class_s GROUP BY id ) AS class_s ON `stu`.`class_id`=`class_s`.`c_id` WHERE `id` = 1 )
 
 # 结果示例
 [
    {
        "id":1,
        "sname":"李广",
        "class_id":1,
        "birthday":"1998-02-12 08:22:13",
        "updated_at":"2021-08-09 20:10:22",
        "sex":"男",
        "cname":"幼儿园",
        "c_id":1
    }
]
```

