# 资料

| 资料                       | 地址                                                         |
| -------------------------- | ------------------------------------------------------------ |
| `EasyWeChat v4.0` 开发文档 | [链接](https://www.bookstack.cn/read/EasyWeChat-v4.0/basic-services-media.md) |
| 官方文档                   | [链接](https://easywechat.com/docs)                          |



# 获取Access Token

- [文档地址](https://www.bookstack.cn/read/EasyWeChat-v4.0/customize-access_token.md)

```php
    protected $app;
    # 依赖注入的方式以便全局调用
    public function __construct()
    {
        # 真实开发记得加缓存不然的话token又请求频率限制,默认一次请求wei两小时
        $this->app = Factory::officialAccount(Config::get('wechat.official_account.default'));
    }
```



# 使用`easywechat`上传临时素材

- [文档地址](https://easywechat.com/docs/5.x/basic-services/media)

 ## 代码示例

```php
     # 1. 文件上传 将文件上传到本地的public目录下然后再进行上传 
        $app = Factory::officialAccount(Config::get('wechat.official_account.default'));
        $file_name = uniqid() . $request->file('media')->getClientOriginalName();//设置唯一的上传图片
        $path = public_path('/');//设置上传路径
        $absolute_path_file = $path . '/' . $file_name;//图片全路径,绝对路径
        $request->file('media')->move($path, $file_name);//转移文件到public目录下
        //判断文件是否存在
        if (!file_exists($absolute_path_file)) {
            return response()->json([
                'msg'=>'文件没有上传成功',
                'data'=>[],
                'code'=>'5000'
            ]);
        }
        $image = $app->media->uploadImage($absolute_path_file);
        unlink($absolute_path_file); //上传完成之后删除临时文件
        dd($image['media_id']);//打印fa媒体id    
```

- 获取临时素材

```php
        $app = Factory::officialAccount(config('wechat.official_account.default'));
        # 获取上传的文件名 例如上传的是 a.jpg  返回 a   
        $file_name = explode('.', $request->file('media')->getClientOriginalName())[0];
        $media_id=  $request->file('media')->getClientOriginalName())['media_id'];
        # easywechat 获取临时素材接口
        $stream = $app->media->get($app->media->uploadImage($media_id);
        if ($stream instanceof \EasyWeChat\Kernel\Http\StreamResponse) {
            // // 以内容 md5 为文件名存到本地
            // $stream->save('保存目录');
            // 自定义文件名，不需要带后缀(开发的时候建议文件名加上时间戳防止重复)
            /**
             * 记住参数1 不要加/,直接会存放在public目录下的downloadm
             */
            $stream->saveAs('download', $file_name);
        }
        // 查询是否保存成功-判断文件是否存在即可
        if (file_exists(public_path('download/'.$request->file('media')->getClientOriginalName()))){
            return 'save_ok';
        }
        return 'save_error';
```



# 菜单管理

## 创建自定义菜单

- 代码示例

```php
  $buttons = [
            [
                "type" => "click",
                "name" => "今日歌曲",
                "key"  => "V1001_TODAY_MUSIC"
            ],
            [
                "name"       => "菜单",
                "sub_button" => [
                    [
                        "type" => "view",
                        "name" => "搜索",
                        "url"  => "http://www.soso.com/"
                    ],
                    [
                        "type" => "view",
                        "name" => "视频",
                        "url"  => "http://v.qq.com/"
                    ],
                    [
                        "type" => "click",
                        "name" => "赞一下我们",
                        "key" => "V1001_GOOD"
                    ],
                ],
            ],
        ];
        $this->app->menu->create($buttons);
```

# easywechat发送模板消息

- [easywechat模板消息](https://easywechat.com/docs/5.x/official-account/template_message#heading-h2-5)

## 微信公众测试平台添加[测试模板消息](https://mp.weixin.qq.com/debug/cgi-bin/sandboxinfo?action=showinfo&t=sandbox/index)

![添加消息模板](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/hqZK4wPxOJ9YzBC.png)

```php
 dd($this->app->template_message->send([
            'touser' => 'o7wV86RHxGwlG_y8fo5-SHd_muZo', #//用户openid
            'template_id' => 'tCsqWfkM6g-tvzlEK4OdgYfRaW_xyIA7vcctJDDUFKw',#//发送的模板id
            'url' => 'www.baidu.com', //发送后用户点击跳转的链接
            'miniprogram' => [ //与公众号绑定的小程序（选传）
                'appid' => '', #//小程序id
                'pagepath' => '', #//跳转页面
            ],
            'data' => [ # 设置的参数 key=>value
                'name' => '姚留洋',   
                'date' => date('Y-m-d',time()),
            ],
        ]));
# 返回结果示例
array:3 [
  "errcode" => 0
  "errmsg" => "ok"
  "msgid" => 1938408526305951753
]
```

- 如果你想为发送的内容字段指定颜色，你可以将 "data" 部分写成下面 4 种不同的样式，不写 `color` 将会是默认黑色：

```php
'data' => [
    'foo' => '你好',  // 不需要指定颜色
    'bar' => ['你好', 'red'], // 指定为红色
    'baz' => ['value' => '你好', 'color' => '#550038'], // 与第二种一样
    'zoo' => ['value' => '你好'], // 与第一种一样
]
```

