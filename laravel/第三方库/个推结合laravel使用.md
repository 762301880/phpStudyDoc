# 资料

> 这期刚好用到了android&ios推送的功能

| name                                 | url                                                          |
| ------------------------------------ | ------------------------------------------------------------ |
| 个推文档中心                         | [link](https://docs.getui.com/getui/server/rest_v2/service_sdk/) |
| ***github***个推***phpsdk***使用文档 | [link](https://github.com/GetuiLaboratory/getui-pushapi-php-client-v2) |
| packagist个推官方安装composer        | [link](https://packagist.org/packages/getuilaboratory/getui-pushapi-php-client-v2) |
| 第三方博客                           | [link](https://www.cnblogs.com/evakang/p/12059920.html)      |
| laravel-个推开发包                   | [link](https://laravelacademy.org/post/9522)                 |



# 使用

## 安装

```shell
composer require getuilaboratory/getui-pushapi-php-client-v2
```

代码示例

```shell
    public static function pushToSingleByCid($title, $body, $cid)
    {
        //创建API，APPID等配置参考 环境要求 进行获取
        $api = new GTClient(config('getui.api_url'), config('getui.app_key'), config('getui.app_id'), config('getui.master_secret'));
        //设置推送参数
        $push = new GTPushRequest();
        $push->setRequestId(self::onlyCode()); # c
        $message = new GTPushMessage();
        $notify = new GTNotification();
        $notify->setTitle($title);
        $notify->setBody($body);
        //点击通知后续动作，目前支持以下后续动作:
        //1、intent：打开应用内特定页面url：打开网页地址。2、payload：自定义消息内容启动应用。3、payload_custom：自定义消息内容不启动应用。4、startapp：打开应用首页。5、none：纯通知，无后续动作
        $notify->setClickType("none");
        $message->setNotification($notify);
        $push->setPushMessage($message);
        $push->setCid($cid);
        //处理返回结果
        $result = $api->pushApi()->pushToSingleByCid($push);
        return $result['code']==false;
    }

    public static function onlyCode()
    {
        return Str::random(15) . strtotime(Carbon::now()->toDateTimeString());
    }
```

[透传](https://github.com/GetuiLaboratory/getui-pushapi-php-client-v2/blob/master/test/PushApiTest.php)

```php
   $api = new \GTClient(config('getui.url'),config('getui.app_key'), config('getui.app_id'),config('getui.master_secret'));
        //设置推送参数
        $push = new \GTPushRequest();
        $push->setRequestId(strtotime(Carbon::now()->toDateTimeString()));
        $message = new \GTPushMessage();
        /*
         * 点击通知后续动作，目前支持以下后续动作:
         * 1intent：打开应用内特定页面url：打开网页地址。
         * 2、payload：自定义消息内容启动应用。
         * 3、payload_custom：自定义消息内容不启动应用。
         * 4、startapp：打开应用首页。5、none：纯通知，无后续动作
         */
        $message->setTransmission("999999999");
        $push->setPushMessage($message);
        //处理返回结果
        $result = $api->pushApi()->pushAll($push);
        dd($result);
# 单推
            $api = new \GTClient(config('getui.url'),config('getui.app_key'), config('getui.app_id'),config('getui.master_secret'));
            //设置推送参数
            $push = new \GTPushRequest();
            $push->setRequestId(strtotime(Carbon::now()->toDateTimeString()));
            $message = new \GTPushMessage();
            /*
             * 点击通知后续动作，目前支持以下后续动作:
             * 1intent：打开应用内特定页面url：打开网页地址。
             * 2、payload：自定义消息内容启动应用。
             * 3、payload_custom：自定义消息内容不启动应用。
             * 4、startapp：打开应用首页。5、none：纯通知，无后续动作
             */
            $data=[
                'message'=>'zhangsan',
                'bo'=>12
            ];
            $message->setTransmission(json_encode($data));
            $push->setCid('48ae473bf5184459b0be69c1e9df91ee');
            $push->setPushMessage($message);
            //处理返回结果
            $result = $api->pushApi()->pushToSingleByCid($push);
            dd($result);
```

## 通知震动等请[查阅文档](https://docs.getui.com/getui/server/rest_v2/common_args/)

![1630386077(1).jpg](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/hK756YkIXrOdtuV.png)

- 代码示例

```php
 $push = new GTPushRequest();
        $push->setRequestId(self::onlyCode());
        $message = new GTPushMessage();
        $notify = new GTNotification();
        $notify->setTitle($this->title);
        $notify->setBody($this->message);
        $notify->setChannelId($this->channelId);#设置通知渠道id，长度 ≤ 64
        $notify->setChannelName($this->channelName); #设置通知渠道名称，长度 ≤ 64
        /***
         设置通知渠道重要性（可以控制响铃，震动，浮动，闪灯等等）
         android8.0以下
         0，1，2:无声音，无振动，不浮动
         3:有声音，无振动，不浮动
         4:有声音，有振动，有浮动
         android8.0以上
         0：无声音，无振动，不显示；
         1：无声音，无振动，锁屏不显示，通知栏中被折叠显示，导航栏无logo;
         2：无声音，无振动，锁屏和通知栏中都显示，通知不唤醒屏幕;
         3：有声音，无振动，锁屏和通知栏中都显示，通知唤醒屏幕;
         4：有声音，有振动，亮屏下通知悬浮展示，锁屏通知以默认形式展示且唤醒屏幕;
        */
        $notify->setChannelLevel($this->channelLevel);# 设置通知渠道等级
        /*
         * 点击通知后续动作，目前支持以下后续动作:
         * 1、intent：打开应用内特定页面url：打开网页地址。
         * 2、payload：自定义消息内容启动应用。
         * 3、payload_custom：自定义消息内容不启动应用。
         * 4、startapp：打开应用首页。5、none：纯通知，无后续动作
         */
        $notify->setClickType("none");
        $message->setNotification($notify);
        $push->setPushMessage($message);
        //处理返回结果
        $this->response = $result = $this->client->pushApi()->pushAll($push);
        return isset($result['code']) && $result['code'] == '0';
```

