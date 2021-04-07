###  参考地址

[laravel8.5文档](https://learnku.com/docs/laravel/8.5/http-client/10390#introduction)







#### 基本使用

> 控制器中使用

```php
        use GuzzleHttp\Client;

        $client = new Client(['verify' => false]);
        $res = $client->request('GET', 'https://www.zhipin.com/wapi/zpCommon/data/oldindustry.json');
        $data=json_decode($res->getBody(),true);
        return response()->json($data);
```