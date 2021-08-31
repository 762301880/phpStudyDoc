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

