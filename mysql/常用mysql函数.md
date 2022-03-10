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

