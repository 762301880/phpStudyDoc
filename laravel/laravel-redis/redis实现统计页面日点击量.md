





参考 https://www.jianshu.com/p/3ea95c0fc814

# 使用

```php
$redis = new Client();
$date=date('Ymd');# 定义存储的名称
$redis->setbit($date,user_id,1);#  第二个参数是用户的id
dd($redis->bitcount($date));# 得到统计的个数
```

