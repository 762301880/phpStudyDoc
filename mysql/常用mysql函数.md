[**返回当年年份**](https://blog.csdn.net/chengmouyu7082/article/details/100910188)

```sql
SELECT DATE_FORMAT(NOW(), '%Y') FROM DUAL ;
```

**时间戳转化为时间**

[参考](https://blog.csdn.net/weixin_34062469/article/details/86361724)

```sql
 # FROM_UNIXTIME('z','%Y-%m-%d')
 FROM_UNIXTIME(pxs_aunt_basics.birth,'%Y-%m-%d') as birth,
```

[**查询表中的字段**](https://blog.csdn.net/Knight_Key/article/details/122565171)

> 查询的是数据库**information_schema**数据库中的**COLUMNS**表,这个在框架中基本无法使用
>
> 因为框架配置具体到了数据库

```php
select COLUMN_NAME from information_schema.COLUMNS where table_name = '具体表名'
```

