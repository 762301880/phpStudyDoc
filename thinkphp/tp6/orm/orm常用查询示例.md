# 说明

> 记录一下常用的逻辑

# [代码示例](https://www.kancloud.cn/cherals/tp51/1004184)

- tp查询指定两列的值

```shell
Student::field(['order_id','serial_sn'])->select();
```

- 获取最新一条记录

```php
$orderModel->first();
```

- 获取最后一条记录

```php
$orderModel->last();
```

- 获取一列的值

```php
$model->column('需要查询的字段');
```

- 向已查询出来的集合中添加数据

```php
 $model->push(['数组类型的数据']);
```

- 关联查询

```php
# 左关联
->leftJoin('ds_group_goods_sku', 'ds_group_goods_order.id=ds_group_goods_sku.group_goods_id')
```

- 或者查询

```php
# or 表示或者
->where("order_state= 12 or order_state= 13");
```

- 不等于

```php
->where('order_state', '<>', '0');
```

- 获取单个属性值

```php
 ->value('price_one');
```

- 模糊查询

```php
 $query->where('order_number', 'like', "%{$order_number}%");
```

- [一个字段查询多个字段](https://www.runoob.com/mysql/mysql-functions.html)

> 先合并多个字段然后再一起查询

![1649667832(1).jpg](https://s2.loli.net/2022/04/11/FM6cNWa3lgRIxB4.png)

```php
//三合一查询(流水号/支付账号/支付单号)
 if (!empty($text)) { //todo 未写完
     $query->where("CONCAT(serial_number,transaction_id) like '%$text%'");
 }
```

