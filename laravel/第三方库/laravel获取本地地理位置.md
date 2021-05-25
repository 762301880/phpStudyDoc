# 说明

> 突然心血来潮想使用php获取自己现在的地理位置，查询腾讯位置服务可以实现自己的需求，于是开始折腾

- 参考资料

| 名称         | 地址                                                         |
| ------------ | ------------------------------------------------------------ |
| 腾讯开放平台 | [链接](https://wiki.open.qq.com/wiki/%E4%BD%8D%E7%BD%AE%E6%9C%8D%E5%8A%A1) |
| 腾讯位置服务 | [链接](https://lbs.qq.com/service/webService/webServiceGuide/webServiceIp) |



# api使用说明

## api说明

> 通过终端设备IP地址获取其当前所在地理位置，精确到市级，常用于显示当地城市天气预报、初始化用户城市等非精确定位场景

- ## 请求URL

  > 该请求为GET请求

```apl
https://apis.map.qq.com/ws/location/v1/ip
```

- 请求参数 

| 参数     | 必填 | 说明                               | 示例                                    |
| :------- | :--- | :--------------------------------- | :-------------------------------------- |
| ip       | 否   | IP地址，缺省时会使用请求端的IP     | ip=111.206.145.41                       |
| key      | 是   | 开发密钥（Key）                    | key=OB4BZ-D4W3U-B7VVO-4PJWW-6TKDJ-WPB77 |
| output   | 否   | 返回格式：支持JSON/JSONP，默认JSON | output=json                             |
| callback | 否   | JSONP方式回调函数                  | callback=function1                      |

- 响应结果

| 名称     | 类型   | 必填   | 说明                                                         |                  |
| :------- | :----- | :----- | :----------------------------------------------------------- | ---------------- |
| status   | number | 是     | 状态码，0为正常, 310请求参数信息有误， 311Key格式错误, 306请求有护持信息请检查字符串, 110请求来源未被授权 |                  |
| message  | string | 是     | 对status的描述                                               |                  |
| result   | object | 是     | IP定位结果                                                   |                  |
|          | ip     | string | 是                                                           | 用于定位的IP地址 |
| location | object | 是     | 定位坐标                                                     |                  |
|          | lat    | number | 是                                                           | 纬度             |
| lng      | number | 是     | 经度                                                         |                  |
| ad_info  | object | 是     | 定位行政区划信息                                             |                  |
|          | nation | string | 是                                                           | 国家             |
| province | string | 是     | 省                                                           |                  |
| city     | string | 否     | 市                                                           |                  |
| district | string | 否     | 区                                                           |                  |
| adcode   | number | 是     | 行政区划代码                                                 |                  |

## 调用示例

```php
// GET请求，注意参数值要进行URL编码
https://apis.map.qq.com/ws/location/v1/ip?ip=111.206.145.41&key=OB4BZ-D4W3U-B7VVO-4PJWW-6TKDJ-WPB77

//响应示例：
{
    "status": 0,
    "message": "query ok",
    "result": {
        "ip": "111.206.145.41",
        "location": {
            "lat": 39.90469,
            "lng": 116.40717
        },
        "ad_info": {
            "nation": "中国",
            "province": "北京市",
            "city": "北京市",
            "district": "",
            "adcode": 110000
        }
    }
}
```



# 使用

## 添加开发者密匙

- [打开并登录腾讯位置服务中心](https://lbs.qq.com/)

- 鼠标放在右上角的头像上，在弹出的下拉菜单中点击`开发者信息`

### 创建新应用

> 点击左侧菜单栏的`应用管理`-`我的应用`=> 创建应用
>
> 填写自己需要的`应用名称`与涉及的`应用类型`

![image-20210525100329368](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210525100329368.png)

### 创建key

> 选择webService即可

![image-20210525100633644](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210525100633644.png)

## 代码中使用示例

- 创建路由

```php
#腾讯位置服务实验
Route::get('/get/location', [LocationController::class, 'index']);
```

- 控制器代码

```php
<?php

namespace App\Http\Controllers;

use App\Http\Controllers\Controller;
use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class LocationController extends Controller
{
    //
    public function index()
    {
        $key = '你自己的key'; # 自己创建的key
        $ip = $request->getClientIp();# 自己的地理位置,$request->getClientIp() 获取设备kip地址(请不要在本地使用)
        $data=Http::get("https://apis.map.qq.com/ws/location/v1/ip?ip={$ip}&key={$key}")->json();# 使用http请求api
        dd($data);#打印输出结果
    }
}

```

- 结果展示

```shell
array:3 [▼
  "status" => 0
  "message" => "query ok"
  "result" => array:3 [▼
    "ip" => "113.78.173.109"
    "location" => array:2 [▼
      "lat" => 23.02067
      "lng" => 113.75179
    ]
    "ad_info" => array:5 [▼
      "nation" => "中国"
      "province" => "广东省"
      "city" => "东莞市"
      "district" => ""
      "adcode" => 441900
    ]
  ]
]
```







