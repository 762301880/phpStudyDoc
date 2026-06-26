## config配置获取失败

> 本地读取可以获取到 但是服务器读取不到信息 **服务器端大小写敏感(操作系统文件系统差异)**

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