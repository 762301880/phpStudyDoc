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
> 这里需要结合上一步的**获取部门列表**接口使用

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

**代码示例**

```shell
```

