## config配置获取失败

> 本地读取可以获取到 但是服务器读取不到信息 因该是服务器端大小写敏感

**配置信息(错误)**

```php
<?php
return [
    'key' => env('gaode_key', ''),   //错误配置必须大写
];
```

**env配置**

```php
#高德地图key
GAODE_KEY=xxxxxxxxxxxxxxxxxx
```

