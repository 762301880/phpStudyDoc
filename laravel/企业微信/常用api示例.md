# 通讯录管理

## [获取部门列表](https://work.weixin.qq.com/api/doc/90000/90135/90208)

> 接口文档地址：https://work.weixin.qq.com/api/doc/90000/90135/90208

```shell
    public function departmentList(Request $request)
    {
        $id=$request->input('id');
        $url="https://qyapi.weixin.qq.com/cgi-bin/department/list?access_token={$this->accessToken}&id={$id}";
        $data=json_decode(file_get_contents($url),true);
        dd($data);
    }
    # 返回示例
    array:3 [
  "errcode" => 0
  "errmsg" => "ok"
  "department" => array:1 [
    0 => array:5 [
      "id" => 1
      "name" => "编程小分队"
      "parentid" => 0
      "order" => 100000000
      "department_leader" => []
    ]
  ]
]
```

## [获取部门成员](https://work.weixin.qq.com/api/doc/90000/90135/90200)

> 接口文档地址：https://work.weixin.qq.com/api/doc/90000/90135/90200
>
> 这里需要结合上一步的**获取部门列表**接口使用,上一步的部门列表会返回id

```shell
    public function simplelist(Request $request)
    {
        $departmentId = $request->input('department_id');//获取的部门id
        if ($departmentId==null) return response()->json(['code'=>'5000','message'=>'获取的部门id不可以为空','data'=>[]]);
        $fetch_child=$request->input('fetch_child')??0;//是否递归获取子部门下面的成员：1-递归获取，0-只获取本部门
        $url = "https://qyapi.weixin.qq.com/cgi-bin/user/simplelist?access_token={$this->accessToken}&department_id={$departmentId}&fetch_child={$fetch_child}";
        $data = json_decode(file_get_contents($url), true);
        dd($data);
    }
   # 返回示例
   array:3 [
  "errcode" => 0
  "errmsg" => "ok"
  "userlist" => array:1 [
    0 => array:3 [
      "userid" => "YaoLiuYang"
      "name" => "姚留洋"
      "department" => array:1 [
        0 => 1
      ]
    ]
  ]
]
```

# 发送应用消息

**资料**

| 名称       | 地址                                                    |
| ---------- | ------------------------------------------------------- |
| 第三方博客 | [link](https://www.mintimate.cn/2021/07/08/pushKitPHP/) |



## [文本消息](https://work.weixin.qq.com/api/doc/90000/90135/90236#%E6%96%87%E6%9C%AC%E6%B6%88%E6%81%AF)

> 接口文档地址:https://work.weixin.qq.com/api/doc/90000/90135/90236#%E6%96%87%E6%9C%AC%E6%B6%88%E6%81%AF
>
> ***说明***
>
> touser 指定接收消息的成员，如果有需要先获取**通讯录管理**的**获取部门列表**→**获取部门成员接口** 需要自定义调用封装**collect()**集合
>
> 封装调用一下
>
> toparty  指定接收消息的部门，如果有需要获取 **获取部门列表**返回

**唯一注意的是企业应用id**

> agentid	是	企业应用的id，整型。企业内部开发，可在应用的设置页面查看；第三方服务商，可通过接口 获取企业授权信息 获取该参数值

<img src="https://s2.loli.net/2021/12/31/gCi7Eh5UXO9DwqS.png" alt="1640912288(1).jpg" style="zoom: 50%;" />

**代码示例**

```php
/**
 *  $agentid 企业应用的id
 */
public function sendMessage($agentid)
    {
        $url = "https://qyapi.weixin.qq.com/cgi-bin/message/send?access_token={$this->getAccessToken()}";
        $data = [
            "touser" => "@all",
            "toparty" => "PartyID1|PartyID2",
            "totag" => "TagID1 | TagID2",
            "msgtype" => "text",
            "agentid" => $agentid,
            "text" => [
                "content" => "你的快递已到，请携带工卡前往邮件中心领取。\n出发前可查看<a href=\"http://work.weixin.qq.com\">邮件中心视频实况</a>，聪明避开排队。"
            ],
            "safe" => 0,
            "enable_id_trans" => 0,
            "enable_duplicate_check" => 0,
            "duplicate_check_interval" => 1800
        ];
        $res=$this->https_request($url,json_encode($data));
        dd($res);
    }
    
    public function https_request($url, $data = null)
    {
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE); //禁止 cURL 验证对等证书
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE); //是否检测服务器的域名与证书上的是否一致
        if (!empty($data)) {
            curl_setopt($curl, CURLOPT_POST, 1);
            curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
        }
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
        $output = curl_exec($curl);
        $error = curl_error($curl);
        curl_close($curl);
        return json_decode($output, true);
    }
```

# [素材管理](https://developer.work.weixin.qq.com/document/path/90253)

## [上传临时素材](https://developer.work.weixin.qq.com/document/path/90253)

**代码示例**

```php
    public function temporaryMaterial($type = 'image')
    {
        $apiUrl = "https://qyapi.weixin.qq.com/cgi-bin/media/upload?access_token={$this->getAccessToken()}&type={$type}";
        $file_name = uniqid() . request()->file('media')->getClientOriginalName();//设置唯一的上传图片

        $path = public_path('/');//设置上传路径
        $absolute_path_file = $path . '/' . $file_name;//图片全路径,绝对路径
        request()->file('media')->move($path, $file_name);//转移文件到public目录下
        //判断文件是否存在
        if (!file_exists($absolute_path_file)) {
            return response()->json([
                'msg' => '文件没有上传成功',
                'data' => [],
                'code' => '5000'
            ]);
        }
        if (class_exists('\CURLFile')) {
            $josn = array( # php5.6以上使用
                'media' => new \CURLFile(realpath($file_name))
            );
        } else { # php 5.6以下使用
            $josn = array('media' => '@' . realpath($file_name));
        }
        $ret = $this->https_request($apiUrl, $josn);
        if (!empty($ret['errmsg'])&&$ret['errmsg']=="ok"&&$ret['errcode']==0){
            unlink($absolute_path_file); # 删除临时文件
        }
        return $ret;
    }
 public function https_request($url, $data = null)
    {
        $curl = curl_init();
        curl_setopt($curl, CURLOPT_URL, $url);
        curl_setopt($curl, CURLOPT_SSL_VERIFYPEER, FALSE); //禁止 cURL 验证对等证书
        curl_setopt($curl, CURLOPT_SSL_VERIFYHOST, FALSE); //是否检测服务器的域名与证书上的是否一致
        if (!empty($data)) {
            curl_setopt($curl, CURLOPT_POST, 1);
            curl_setopt($curl, CURLOPT_POSTFIELDS, $data);
        }
        curl_setopt($curl, CURLOPT_RETURNTRANSFER, true);
        $output = curl_exec($curl);
        $error = curl_error($curl);
        curl_close($curl);
        return json_decode($output, true);
    }
```

