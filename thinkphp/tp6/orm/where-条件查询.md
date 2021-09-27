

# 资料

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [https://blog.csdn.net/lunsunhuan1825/article/details/73467368] |
|          |                                                              |

# 代码示例

```php
EQ  = 等于

NEQ  <> 不等于

LT < 大于

ELT <= 大于等于

GT > 小于

EGT >= 小于等于

BETWEEN BETWEEN * AND * 在两者之间

NOTBETWEEN NOT BETWEEN * AND * 不在两者之间

IN IN (*,*) 在两者之间

NOTIN NOT IN (*,*) 不在两者之间
# orWhere 查询 等同于sql where (order_state=12 OR order_id =13)
->where('order_state=12 or order_state=13')     
```

