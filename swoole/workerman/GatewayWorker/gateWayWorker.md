# 一、简介

> GatewayWorker是基于Workerman开发的	
>
> GatewayWorker基于Workerman开发的一个项目框架，用于快速开发长连接应用，例如app推送服务端、即时IM服务端、游戏服务端、物联网、智能家居等等。
> GatewayWorker使用经典的Gateway和Worker进程模型。Gateway进程负责维持客户端连接，并转发客户端的数据给Worker进程处理；Worker进程负责处理实际的业务逻辑，并将结果推>送给对应的客户端。Gateway服务和Worker服务可以分开部署在不同的服务器上，实现分布式集群。
> GatewayWorker提供非常方便的API，可以全局广播数据、可以向某个群体广播数据、也可以向某个特定客户端推送数据。配合Workerman的定时器，也可以定时推送数据。

- 参考资料

| 名称                      | 地址                                                         |
| ------------------------- | ------------------------------------------------------------ |
| GatewayWorker2.x 3.x 手册 | [链接](http://doc2.workerman.net/)                           |
| 安装文档                  | [链接](https://github.com/walkor/GatewayClient)              |
| workerman-后盾人文档      | [链接](https://doc.houdunren.com/%E6%8F%92%E4%BB%B6%E6%89%A9%E5%B1%95/Laravel/4%20workerman.html#%E5%B8%B8%E7%94%A8%E5%91%BD%E4%BB%A4) |

## 1.1安装

>安装内核
>只安装GatewayWorker内核文件（不包含start_gateway.php start_businessworker.php等启动入口文件）

- 使用`composer`在项目中安装

```shell
composer require workerman/gatewayclient
```

# 二、使用

## 2.1要使用GatewayWorker首先需要下载GatewayWorker包

[下载地址](https://www.workerman.net/download)

![image-20210609141807052](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210609141807052.png)

## 2.2 解压到项目中

- 可以放在任意位置中
- windows请双击使用`start_for_win.bat`启用项目

## 2.3重要的事情说三遍

业务开发只需要关注 Applications/项目/Events.php一个文件即可。
业务开发只需要关注 Applications/项目/Events.php一个文件即可。
业务开发只需要关注 Applications/项目/Events.php一个文件即可。

## 2.4 开发需要在框架的Events中改写代码以便结合框架使用

- 此处请参考官方手册[与ThinkPHP等框架结合](http://doc2.workerman.net/work-with-other-frameworks.html)

```php
public static function onConnect($client_id)
    {
        Gateway::sendToClient($client_id, json_encode(array(
        'type'      => 'init',
        'client_id' => $client_id
     )));
    }
```

- 流程

>  前端在此处连接websocket然后,后端会返回一条json数据前端，前端使用switch判断是否已经初始化，
>
> 如果返回数据有init则代表连接websocket成功，此时前端需要拿到传输过来的client_id与用户表中的用户
>
> 表中的id进行绑定，也就是与用户进行绑定，此时前段用返回的数据进行switch判断如果是init则请求后端的
>
> 绑定用户数据接口，前端需要把拿到的client_id传输过去,

例子:

- 路由

```php
Route::get('bindUid','BindUidController@bind');
```

- 控制器

> 这里先绑定前端传输过来的client_id与数据库中的id,再将用户的数据传输给前端,
>
> $data里的type是用于前段判断是某种类型

```php
use GatewayClient\Gateway;
use Illuminate\Http\Request;
public function __construct()
{
        //设置Gatewayworker服务的register服务与ip和端口
        Gateway::$registerAddress='127.0.0.1:8282';
}
public function bind(Request $request)
{
  $uid='用户id';//获取用户id赋值给变量
  $client_id=$request->get('client_id');
  Gateway::bindUid($client_id,$uid);//绑定用户id  参数1 前端传输过来的clientid 参数2 需要绑定的当前的用户id
   $data = [
            'type' => 'type',
            'data' => [
                '用户信息1' => '用户信息1',
                '用户信息2' => '用户信息2',
                '用户信息3' => '用户信息3'
            ]
        ];
    //绑定完成之后再将用户的数据发送给前端  
    Gateway::sendToAll(json_encode($data));  
}
```



# [tp5 Workerman 快速上手手册](https://www.kancloud.cn/thinkphp/think-worker/722897#Workerman_Server_389)





























