

# 资料

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link][https://blog.csdn.net/lunsunhuan1825/article/details/73467368]   [link](https://www.jb51.net/article/110112.htm) |
| 时间查询 | [link](https://www.kancloud.cn/manual/thinkphp5_1/354029)    |

# [代码示例](https://www.kancloud.cn/manual/thinkphp5_1/354004)

[**参考1**](https://www.kancloud.cn/manual/thinkphp5_1/354085)  [**参考2**](https://www.kancloud.cn/manual/thinkphp5_1/354107)

```php
EQ  =     # 等于

NEQ  <>   # 不等于

LT <      # 小于

ELT <=    # 小于等于

GT >      # 大于

EGT >=    # 大于等于

BETWEEN BETWEEN * AND *  # 在两者之间

NOTBETWEEN NOT BETWEEN * AND * # 不在两者之间

IN IN (*,*) # 在两者之间

NOTIN NOT IN (*,*) # 不在两者之间
# orWhere 查询 等同于sql where (order_state=12 OR order_id =13)
->where('order_state=12 or order_state=13')     
```

