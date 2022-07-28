## [**返回当年年份**](https://blog.csdn.net/chengmouyu7082/article/details/100910188)

```sql
SELECT DATE_FORMAT(NOW(), '%Y') FROM DUAL ;
```

## **时间戳转化为时间**

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_34062469/article/details/86361724) |

**代码示例**

```sql
 # FROM_UNIXTIME('z','%Y-%m-%d')
 FROM_UNIXTIME(pxs_aunt_basics.birth,'%Y-%m-%d') as birth,
```

## [**查询表中的字段**](https://blog.csdn.net/Knight_Key/article/details/122565171)

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.yisu.com/zixun/693352.html) [link](https://blog.csdn.net/qq_28466271/article/details/102503137) [link](https://9iphp.com/web/php/laravel-get-column-data-type.html) |

**说明**

> 查询的是数据库**information_schema**数据库中的**COLUMNS**表,这个在框架中基本无法使用
>
> 因为框架配置具体到了数据库

```php
select COLUMN_NAME from information_schema.COLUMNS where table_name = '具体表名'

    # thinkphp  TABLE_SCHEMA="数据库名称",TABLE_NAME="数据库下的表名称"
    $res=Db::query("SELECT COLUMN_NAME FROM information_schema.columns WHERE TABLE_SCHEMA='laravel_study' AND TABLE_NAME='stu'");
    $columbs=array_column($res,"COLUMN_NAME");
    dd($columbs); # 返回对应字段名称
```

**自定义orm封装**

> **使用**:可以放在**orm模型**中或者写一个**trait**模型继承使用

```php
# thinkphp 
    /**
     * 返回所有的字段名称
     * 缺点必须有一条记录才可以 需要排除的字段
     * @param array $exceptField
     * @return array
     */
    public static function getFieldNames($exceptField = [])
    {
        $selfModel = self::field($exceptField, true)->find();
        $selfModel = !empty($selfModel) ? $selfModel->toArray() : [];
        return array_keys($selfModel);
    }
```

## 替换字段的部分内容

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/qq_42640067/article/details/118070848) |

**说明**

> **核心思想**
>
> 利用mysql的**REPLACE**函数做替换字符
>
> ```mysql
> REPLACE(s,s1,s2)	将字符串 s2 替代字符串 s 中的字符串 s1	
> 将字符串 abc 中的字符 a 替换为字符 x：    SELECT REPLACE('abc','a','x') --xbc
> ```

![image-20220728113847340](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220728113847340.png)

**代码示例**

```shell
UPDATE "表名" SET "需要修改的字段名称" = REPLACE ( "需要修改的字段名称", "需要修改的文字(例如:咨询)", "修改后的文字(例如:询问)" ) WHERE cate = "表名"
```

