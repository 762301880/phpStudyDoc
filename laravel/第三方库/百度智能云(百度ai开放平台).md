# [获取access_token](https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu)

> 通过API Key和Secret Key获取的access_token,参考“[Access Token获取](https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu)”, 调用API时必须在URL中带上access_token参数,需要调用百度api开放平台的接口很多地方都需要这个鉴权,所以这个可以当作一个全局的函数以便全局调用

## 获取Access Token

**请求URL数据格式**

向授权服务地址`https://aip.baidubce.com/oauth/2.0/token`发送请求（推荐使用POST），并在URL中带上以下参数：

- **grant_type：** 必须参数，固定为`client_credentials`；(写死即可)
- **client_id：** 必须参数，应用的`API Key`；(调用的当前应用的client_id)
- **client_secret：** 必须参数，应用的`Secret Key`；(调用的当前应用的client_secret)

## 资料

| 名称     | 地址                                                    |
| -------- | ------------------------------------------------------- |
| 官方文档 | [link](https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu) |

## 代码示例

> 具体参数可以查阅文档

```php
public function getAccessToken()
    {
        $client_id = "应用的API_Key";
        $client_secret = "应用的Secret_Key";
        $url = "https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id={$client_id}&client_secret={$client_secret}";
        $originRes = file_get_contents($url);
        if (empty($originRes)) {
            return response()->json(['code' => '5000', 'message' => '获取access_token失败', 'data' => $originRes]);
        }
        # 如果缓存存在获取缓存中的token
        $access_token = \Cache::get('access_token');
        if ($access_token) {
            return $access_token;
        }
        $res = json_decode($originRes, true);
        $access_token = $res['access_token'];
        $CacheExpirationTime = 3600 * 24 * 10;//缓存过期时间10天
        \Cache::set('access_token', $access_token, $CacheExpirationTime);//保存缓存
        return $access_token;
    }
```



# **人脸对比**

## 百度图像人脸对比

- 资料

| name                                      | url                                                          |
| ----------------------------------------- | ------------------------------------------------------------ |
| 在线图片Base64编码,base64在线图片转换工具 | [link](https://oktools.net/image2base64),[link](http://tool.chinaz.com/tools/imgtobase/) |
| 百度ai人脸对比api文档                     | [link](https://cloud.baidu.com/doc/FACE/s/Lk37c1tpf)         |

- 代码示例

```php
    public function faceContrast(Request $request)
    {
        $image_json =
            [
                [
                    "image" => base64_encode($request->file('image1')->get()), #得到不包含data:image/jpeg;base64,开头的base64加密
                    "image_type" => "BASE64",
                    "face_type" => "LIVE",
                    "quality_control" => "LOW",
                    "liveness_control" => "NONE"
                ],
                [
                    "image" =>base64_encode($request->file('image2')->get()),
                    "image_type" => "BASE64",
                    "face_type" => "LIVE",
                    "quality_control" => "LOW",
                    "liveness_control" => "NONE"
                ]
            ];

        $access_token = Http::get('https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id=b8jmx9vjyvoNUnSvTjmj6B31&client_secret=rsRDlmL8vy00SbDw0tLgKCkntSZchpRd&')->json()['access_token'];
        $url = "https://aip.baidubce.com/rest/2.0/face/v3/match?access_token={$access_token}";
        $data = Http::post($url,$image_json)->json();
        dd($data);
    }
```

- 结果示例

```shell
array:6 [
  "error_code" => 0
  "error_msg" => "SUCCESS"
  "log_id" => 1545550019400
  "timestamp" => 1627004838
  "cached" => 0
  "result" => array:2 [
    "score" => 95.85346222 # 得到人脸识别对比分数
    "face_list" => array:2 [
      0 => array:1 [
        "face_token" => "0799143db5ce11b13a4607d1a67d1bf4"
      ]
      1 => array:1 [
        "face_token" => "c9799cbed5103f67174250bc1b7613b2"
      ]
    ]
  ]
]
```

# 图像增强与特效

##  黑白图像上色

### 说明&资料

- 说明

> 采用[百度AI开放平台](https://ai.baidu.com/tech/imageprocess/colourize)`黑白图像上色`接口

- 参考资料

| 名称                                      | 地址                                                         |
| :---------------------------------------- | :----------------------------------------------------------- |
| 图像增强与特效文档                        | [链接](https://cloud.baidu.com/doc/IMAGEPROCESS/index.html)  |
| 黑白图像上色                              | [链接](https://cloud.baidu.com/doc/IMAGEPROCESS/s/Bk3bclns3) |
| 在线图片Base64编码,base64在线图片转换工具 | [link](https://oktools.net/image2base64),[link](http://tool.chinaz.com/tools/imgtobase/) |

获取`access_token`

[官方文档](https://ai.baidu.com/ai-doc/REFERENCE/Ck3dwjhhu)

获取Access Token

**请求URL数据格式**

向授权服务地址`https://aip.baidubce.com/oauth/2.0/token`发送请求（推荐使用POST），并在URL中带上以下参数：

- **grant_type：** 必须参数，固定为`client_credentials`；
- **client_id：** 必须参数，应用的`API Key`；
- **client_secret：** 必须参数，应用的`Secret Key`；

### 代码示例

> 代码参考官方php[示例](https://cloud.baidu.com/doc/IMAGEPROCESS/s/2k3bclou0)

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\Http;

class TestController extends Controller
{
    public function upload(Request $request)
    {
        if (!$request->isMethod('post')) {
            return response()->json(['msg'=>'必须是post请求','data'=>[],'code'=>5000]);
        }
        $token = $this->getAccessToken();
        $url = 'https://aip.baidubce.com/rest/2.0/image-process/v1/colourize?access_token=' . $token;
        $img = base64_encode($request->file('file')->get()); # 获取bese64加密图片
        $bodys = array(
            'image' => $img
        );
        $res = json_decode($this->request_post($url, $bodys), true);
        dd($res['image']);
    }

    function request_post($url = '', $param = '')
    {
        if (empty($url) || empty($param)) {
            return false;
        }
        $postUrl = $url;
        $curlPost = $param;
        // 初始化curl
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $postUrl);
        curl_setopt($curl, CURLOPT_HEADER, 0);
        // 要求结果为字符串且输出到屏幕上
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
        // post提交方式
        curl_setopt($curl, CURLOPT_POST, 1);
        curl_setopt($curl, CURLOPT_POSTFIELDS, $curlPost);
        // 运行curl
        $data = curl_exec($curl);
        curl_close($curl);
        return $data;
    }
    # 获取access_token
    protected function getAccessToken()
    {
        $API_Key = "****************";#你的$API_Key
        $Secret_Key = "****************";#你的$Secret_Key
        $api = "https://aip.baidubce.com/oauth/2.0/token?grant_type=client_credentials&client_id={$API_Key}&client_secret={$Secret_Key}";
        $data = Http::post($api)->json();
        return $data['access_token'];
    }
}

```

# 文字识别

## 资料

| 名称     | 链接                                                         |
| -------- | ------------------------------------------------------------ |
| 官网地址 | [link](https://console.bce.baidu.com/ai/?_=1641191558414#/ai/ocr/overview/index) |
| 官网文档 | [link](https://cloud.baidu.com/doc/OCR/index.html)           |

​	

## 通用场景文字识别

### [通用文字识别（高精度版）](https://cloud.baidu.com/doc/OCR/s/1k3h7y3db)

**代码示例**

```shell
 public function discern(Request $request)
    {
        $token = $this->getAccessToken();
        $url = 'https://aip.baidubce.com/rest/2.0/ocr/v1/accurate_basic?access_token=' . $token;
        # 文件上传
        $file = $request->file('file');
        if (empty($file)) {
            return response()->json([
                'code' => '5000',
                'message' => '需要上传的文件不可以为空',
                'data' => []
            ]);
        }
        $file_name = uniqid() . $file->getClientOriginalName();//设置唯一的上传图片
        $path = public_path('/');//设置上传路径
        $absolute_path_file = $path . '/' . $file_name;//图片全路径,绝对路径
        $file->move($path, $file_name);//转移文件到public目录下
        //判断文件是否存在
        if (!file_exists($absolute_path_file)) {
            return response()->json([
                'msg' => '文件没有上传成功',
                'data' => [],
                'code' => '5000'
            ]);
        }
        $img = file_get_contents($absolute_path_file);
        $img = base64_encode($img);
        $bodys = array(
            'image' => $img
        );
        $res = $this->request_post($url, $bodys);
        var_dump($res);
    }

    /**
     * 发起http post请求(REST API), 并获取REST请求的结果
     * @param string $url
     * @param string $param
     * @return - http response body if succeeds, else false.
     */
    public function request_post($url = '', $param = '')
    {
        if (empty($url) || empty($param)) {
            return false;
        }

        $postUrl = $url;
        $curlPost = $param;
        // 初始化curl
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $postUrl);
        curl_setopt($curl, CURLOPT_HEADER, 0);
        // 要求结果为字符串且输出到屏幕上
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, 1);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, false);
        // post提交方式
        curl_setopt($curl, CURLOPT_POST, 1);
        curl_setopt($curl, CURLOPT_POSTFIELDS, $curlPost);
        // 运行curl
        $data = curl_exec($curl);
        curl_close($curl);

        return $data;
    }
```

