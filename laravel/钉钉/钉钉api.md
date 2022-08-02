# **钉钉机器人**

**说明**

> 看见了钉钉自带的钉钉机器人挺好玩的想着的能不能使用自己的服务器推送消息玩
>
> 百度了一番果然可以于是先玩一波

**资料**

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 钉钉开放文档           | [钉钉机器人](https://open.dingtalk.com/document/robots/github-robot) |
| 自定义机器人文档(官方) | [link](https://open.dingtalk.com/document/group/custom-robot-access) |
| 参考博客               | [link ](https://blog.csdn.net/qq_34193883/article/details/106471299) [link](https://www.jianshu.com/p/cc42a7915acc) |

## 钉钉机器人api消息推送

![1640845383.jpg](https://s2.loli.net/2021/12/30/4aCWK3JfAmRbr9h.png)

> ## 加签
>
> 把`timestamp+"\n"+`密钥当做签名字符串，使用HmacSHA256算法计算签名，然后进行Base64 encode，最后再把签名参数再进行urlEncode，得到最终的签名（需要使用UTF-8字符集）。

```php
public function send(Request $request)
    {
        $url = '你的Webhook';
        $time = time() * 1000;//毫秒级时间戳
        $secret = '你的签名密匙';
        $sign = hash_hmac('sha256', $time . "\n" . $secret, $secret, true);
        $sign = base64_encode($sign);
        $sign = urlencode($sign);
        $msg = [
            'msgtype' => 'text',//这是文件发送类型，可以根据需求调整
            'text' => [
                'content' => '这是需要发送的内容',
            ],
             //具体通知到用户
            'at' => [
                //"atMobiles" => ['17538397579'],
                "atUserIds" => ['226504530523064108'],//userid
            ]
        ];
        $url = "{$url}&timestamp={$time}&sign={$sign}";
        $res=$this->request_by_curl($url,$msg);
        dd($res);
    }
   public function request_by_curl($remote_server, $post_string) {
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $remote_server);
        curl_setopt($ch, CURLOPT_POST, 1);
        curl_setopt($ch, CURLOPT_CONNECTTIMEOUT, 5);
        curl_setopt($ch, CURLOPT_HTTPHEADER, array ('Content-Type: application/json;charset=utf-8'));
        curl_setopt($ch, CURLOPT_POSTFIELDS, json_encode($post_string));
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        // 线下环境不用开启curl证书验证, 未调通情况可尝试添加该代码
        curl_setopt ($ch, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt ($ch, CURLOPT_SSL_VERIFYPEER, 0);
        $data = curl_exec($ch);
        curl_close($ch);
        return $data;
    }
```

# 扩展补充

## 钉钉查看员工UserId

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/u013727805/article/details/120383024) |

[点击跳转地址](https://oa.dingtalk.com/contacts.htm#/contacts?_k=k119su)

> 点击部门人员，右侧弹出的信息旁边有用户id

![image-20220802104448667](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220802104448667.png)