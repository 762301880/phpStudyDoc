###  参考地址

* [laravel8.5文档](https://learnku.com/docs/laravel/8.5/http-client/10390#introduction)
* [后盾人](https://houdunren.gitee.io/note/%E6%89%8B%E5%86%8C/laravel%208.x/6%20%E7%BB%BC%E5%90%88%E7%9F%A5%E8%AF%86/10%20HTTP%E5%AE%A2%E6%88%B7%E7%AB%AF.html)

#### 基本使用



> 控制器中使用

```php
        use GuzzleHttp\Client;

        $client = new Client(['verify' => false]);
        $res = $client->request('GET', 'https://www.zhipin.com/wapi/zpCommon/data/oldindustry.json');
        $data=json_decode($res->getBody(),true);
        return response()->json($data);
```