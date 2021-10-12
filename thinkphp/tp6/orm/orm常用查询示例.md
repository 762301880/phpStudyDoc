# 说明

> 记录一下常用的逻辑

# 代码示例

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

