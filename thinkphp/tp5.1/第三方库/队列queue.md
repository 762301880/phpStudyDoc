# 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link ](https://blog.csdn.net/will5451/article/details/80434174)  [link](https://www.jianshu.com/p/9ceebfb6ade0)  [link](http://t.zoukankan.com/myvic-p-9373710.html) |
| github官方文档 | [link](https://github.com/top-think/think-queue/tree/2.0)    |

#  安装&使用

## **安装**

> 使用composer 命令安装

```php
composer require topthink/think-queue=2.*
```

**配置文件**

>配置文件位于 `config/queue.php`
>
>**驱动配置**
>
>各个驱动的具体可用配置项在`think\queue\connector`目录下各个驱动类里的`options`属性中，写在上面的`queue`配置里即可覆盖
>
>项目/vendor/topthink/think-queue/src/queue/connector/Redis.php

公共配置

> 推荐使用**redis**配置

```php
# 原本自带默认配置
[
    'connector'=>'sync' //驱动类型，可选择 sync(默认):同步执行，database:数据库驱动,redis:Redis驱动,topthink:Topthink驱动
                   //或其他自定义的完整的类名
]
# 对应配置如下
return [
       'connector'  => 'Redis',         // Redis 驱动
       'expire'     => 60,              // 任务的过期时间，默认为60秒; 若要禁用，则设置为 null 
       'default'    => 'default',       // 默认的队列名称
       'host'       => '127.0.0.1',     // redis 主机ip
       'port'       => 6379,            // redis 端口
       'password'   => '',              // redis 密码
       'select'     => 1,               // 使用哪一个 db，默认为 db0
       'timeout'    => 0,               // redis连接的超时时间
       'persistent' => false,           // 是否是长连接
     
   //    'connector' => 'Database',   // 数据库驱动
   //    'expire'    => 60,           // 任务的过期时间，默认为60秒; 若要禁用，则设置为 null
   //    'default'   => 'default',    // 默认的队列名称
   //    'table'     => 'jobs',       // 存储消息的表名，不带前缀
   //    'dsn'       => [],

   //    'connector'   => 'Topthink',   // ThinkPHP内部的队列通知服务平台 ，本文不作介绍
   //    'token'       => '',
   //    'project_id'  => '',
   //    'protocol'    => 'https',
   //    'host'        => 'qns.topthink.com',
   //    'port'        => 443,
   //    'api_version' => 1,
   //    'max_retries' => 3,
   //    'default'     => 'default',

   //    'connector'   => 'Sync',       // Sync 驱动，该驱动的实际作用是取消消息队列，还原为同步执行
   ];
```



## 使用

**创建任务类**

> 单模块项目推荐使用 `app\job` 作为任务类的命名空间 多模块项目可用使用 `app\module\job` 作为任务类的命名空间 
>
> 也可以放在任意可以自动加载到的地方
>
> 任务类不需继承任何类，如果这个类只有一个任务，那么就只需要提供一个`fire`方法就可以了，如果有多个小任务，就写多个方法，下面发布任务的时候会有区别
> 每个方法会传入两个参数 `think\queue\Job $job`（当前的任务对象） 和 `$data`（发布任务时自定义的数据）
>
> 还有个可选的任务失败执行的方法 `failed` 传入的参数为`$data`（发布任务时自定义的数据）

### 下面写两个例子

```php
namespace app\job;

use think\queue\Job;

class Job1{
    
    public function fire(Job $job, $data){
    
            //....这里执行具体的任务 
            
             if ($job->attempts() > 3) {
                  //通过这个方法可以检查这个任务已经重试了几次了
             }
            
            
            //如果任务执行成功后 记得删除任务，不然这个任务会重复执行，直到达到最大重试次数后失败后，执行failed方法
            $job->delete();
            
            // 也可以重新发布这个任务
            $job->release($delay); //$delay为延迟时间
          
    }
    
    public function failed($data){
    
        // ...任务达到最大重试次数后，失败了
    }

}
namespace app\lib\job;

use think\queue\Job;

class Job2{
    
    public function task1(Job $job, $data){
    
          
    }
    
    public function task2(Job $job, $data){
    
          
    }
    
    public function failed($data){
        
    }

}
```

## 发布任务

> `think\Queue::push($job, $data = '', $queue = null)` 和 `think\Queue::later($delay, $job, $data = '', $queue = null)` 两个方法，前者是立即执行，后者是在`$delay`秒后执行

```php
$job` 是任务名
单模块的，且命名空间是`app\job`的，比如上面的例子一,写`Job1`类名即可
多模块的，且命名空间是`app\module\job`的，写`model/Job1`即可
其他的需要些完整的类名，比如上面的例子二，需要写完整的类名`app\lib\job\Job2`
如果一个任务类里有多个小任务的话，如上面的例子二，需要用@+方法名`app\lib\job\Job2@task1`、`app\lib\job\Job2@task2
```

`$data` 是你要传到任务里的参数

`$queue` 队列名，指定这个任务是在哪个队列上执行，同下面监控队列的时候指定的队列名,可不填

## 监听任务并执行

> php think queue:listen

> php think queue:work --daemon（不加--daemon为执行单个任务）

两种，具体的可选参数可以输入命令加 --help 查看

> 可配合supervisor使用，保证进程常驻

# 实战示例

## 使用

### 定义队列

> jurisdiction可以看上面的示例

###  生产队列

```php
    /**
     * 后台创建预约订单
     * @param $data
     */
    public function createOrderServiceReserve($data)
    {

        $order_number = $data['order_number'] ?? "";
        $remark = $data['remark'] ?? "";
        $is_fixed_aunt = $data['is_fixed_aunt'] ?? "";
        $reserve_times = $data['reserve_times'] ?? "";
        $reservation_time = !empty($reserve_times) ? $reserve_times['reservation_time'] : "";
        $start_time = !empty($reserve_times) ? $reserve_times['start_time'] : "";
        $end_time = !empty($reserve_times) ? $reserve_times['end_time'] : "";
        $aunt_id = $data['aunt_id'] ?? "";
        $auntModel = AuntModel::get($aunt_id);
        Db::startTrans();
        $orderServiceReserve = new OrderServiceReserveModel();
        $orderServiceReserve->reservation_number = OrderServiceReserveModel::generatereServationNumber();//生成预约编号
        $orderModelInfo = $this->queryOrderNumber($order_number);
        if ($orderModelInfo->status == OrderModel::STATUS_PENDING_PAYMENT) throw new SystemException("待支付的订单不可以添加预约");
        $orderServiceReserve->user_name = $orderModelInfo->user_name ?? "";
        $orderServiceReserve->order_id = $orderModelInfo->id;
        $orderServiceReserve->user_phone = $orderModelInfo->phone ?? "";
        $orderServiceReserve->service_name = $orderModelInfo->service_name ?? "";
        $orderServiceReserve->service_id = $orderModelInfo->service_id ?? 0;
        $orderServiceReserve->to_door_address = $orderModelInfo->to_door_address ?? "";
        $orderServiceReserve->reservation_time = $reservation_time;
        $orderServiceReserve->start_time = $start_time;
        $orderServiceReserve->end_time = $end_time;
        $orderServiceReserve->hours = $orderModelInfo->hours ?? 0;
        $orderServiceReserve->aunt_name = $auntModel->name ?? "";
        $orderServiceReserve->aunt_id = $auntModel->id ?? 0;
        $orderServiceReserve->residence_area = $orderModelInfo->residence_area ?? "";
        if (!empty($auntModel)) {
            $orderServiceReserve->aunt_name = !empty($auntModel) ? $auntModel->name : "";
            $orderServiceReserve->aunt_id = !empty($auntModel) ? $auntModel->id : 0;
        }
        $orderServiceReserve->status = OrderServiceReserveModel::STATUS_STAY_SERVICE;
        $orderServiceReserve->is_fixed_aunt = $is_fixed_aunt;
        $orderServiceReserve->remark = $remark;
        if (!$orderServiceReserve->save()) {
            Db::rollback();
            return false;
        }
        # 添加阿姨相关
        $data['order_service_reserve_id'] = $orderServiceReserve->id;
        $this->addOrderReserveAunts(OrderModel::get($orderModelInfo->id), $data);
        Db::commit();
        Queue::push(SendOrderReserveNoticeJob::class, ['order_service_reserve_id' => $orderServiceReserve->id],'reserve_notice');//推送模板消息
        return true;
    }
```



###  消费队列

```php
<?php


namespace app\common\job;


use app\admin\model\AdminModel;
use app\common\model\OrderServiceReserveModel;
use app\common\service\OfficialAccountService;
use app\common\service\OfficialAccountTemplateService;
use think\Db;
use think\facade\Log;
use think\queue\Job;

class SendOrderReserveNoticeJob
{
    public function fire(Job $job, $data)
    {
        $orderServiceReserve_id = $data['order_service_reserve_id'] ?? 0;
        Log::info("发送预约通知队列,预约主键:" . json_encode($orderServiceReserve_id) . date('Y-m-d H:i:s'));
        Db::startTrans();
        # 加锁预防队列执行过快并发情况导致查询预约为空删除了队列导致队列未执行全
        $orderServiceReserveModel = OrderServiceReserveModel::lock(true)->get($orderServiceReserve_id);
        if (empty($orderServiceReserveModel)) {
            Log::info("预约信息为空中断本次预约:" . json_encode($orderServiceReserveModel));
            $job->delete();
        }
        $openidS = $this->getOpenidS();
        foreach ($openidS as $value) {
            $this->sendTemplateNotice($orderServiceReserveModel, $value);
        }
        Db::commit();
        //如果任务执行成功后 记得删除任务，不然这个任务会重复执行，直到达到最大重试次数后失败后，执行failed方法
        $job->delete();
    }

    /**
     * 发送模板通知
     * @param OrderServiceReserveModel $orderServiceReserveModel
     * @param $openId
     * @return bool
     */
    public function sendTemplateNotice(OrderServiceReserveModel $orderServiceReserveModel, $openId)
    {
        $template = OfficialAccountTemplateService::getNewOrderNotice($orderServiceReserveModel, $openId);
        Log::info("已获取模板信息:" . json_encode($template));
        $bool = (new OfficialAccountService())->sendTemplateMessage($template);
        Log::info("发送模板信息成功");
        return $bool;
    }

    public function getOpenidS()
    {
        $openIds = AdminModel::where(['status' => AdminModel::STATUS_ENABLE])
            ->where("official_account_openid", 'NEQ', "")
            ->column('official_account_openid');
        Log::debug("已获取的openid列表:" . json_encode($openIds));
        return $openIds;
    }
}
```

## supervisor配置

**参考**

| 名称                        | 地址                                                         |
| --------------------------- | ------------------------------------------------------------ |
| laravel-队列 supervisor配置 | [link](https://learnku.com/docs/laravel/8.x/queues/9398#e45763) |

**个人配置示例**

> 创建**send_notice.ini**配置文件 创建位置`/etc/supervisord.d`

```shell
[program:send_notice]
process_name=%(program_name)s_%(process_num)02d
# --queue reserve_notice  监听指定的队列
command=php  /www/wwwroot/home_train/think queue:work --daemon --queue reserve_notice     
autostart=true
autorestart=true
startsecs=0
# 修改为当前登录的用户 可以用 whoami 命令查询
user=root 
numprocs=1
redirect_stderr=true
stdout_logfile=/etc/supervisord.d/log/send_notice.log #日志地址(目录需要自定义不会自动创建)
stopwaitsecs=3600
```

### **Linux环境 tp5.1 Could not open input file: think**

**参考资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_41406041/article/details/100571601?spm=1001.2101.3001.6650.6&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-100571601-blog-113943124.pc_relevant_multi_platform_whitelistv1&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-6-100571601-blog-113943124.pc_relevant_multi_platform_whitelistv1&utm_relevant_index=9) |

**解决方案**

```shell
# 采用work进程
#command=php  /www/wwwroot/home_train/think queue:listen 废弃
command=php  /www/wwwroot/home_train/think queue:work --daemon
```

# 扩展补充

## 杀掉队列进程

```shell
# 查询进程  找到COMMAND 等于自己设置的命令
[root@PXS-TEST ~]# ps -aux
......
php /www/wwwroot/home_train/think queue:work --daemon
......
# kill掉对应的PID即可
kill PID 
```

## 如果测试服务器已经挂起监听队列本地不要再监听

> 坑啊,这两天在做队列相关发现了一个坑，就是队列怎么执行每次都少了好多(**忘记了redis监听队列测试服已经处于监听，然后本地想着调试写入日志就本地也开了监听**)，一直以为是循环中请求队列并发导致
>
> 最后无意中发现把本地监听停了之后,队列还在执行，这时候恍然大悟，原来测试服的队列还在监听着reids中写入的任务

# bug解决

## 队列导致MySQL server has gone away

**参考资料**

| 名称                                     | 地址                                                         |
| ---------------------------------------- | ------------------------------------------------------------ |
| 博客                                     | [link](https://blog.csdn.net/thepatterraining/article/details/108360179)  [link](https://blog.csdn.net/weixin_33914982/article/details/113317790?spm=1001.2101.3001.6650.3&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-113317790-blog-108360179.pc_relevant_multi_platform_featuressortv2dupreplace&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-3-113317790-blog-108360179.pc_relevant_multi_platform_featuressortv2dupreplace&utm_relevant_index=4)  [link](https://blog.csdn.net/weixin_42116596/article/details/113598699?spm=1001.2101.3001.6650.2&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-113598699-blog-78840538.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-2-113598699-blog-78840538.pc_relevant_aa&utm_relevant_index=3) |
| mysql排查-server has gone away  参考资料 | [link](https://blog.csdn.net/LXLXLJLJ/article/details/117739733)  [link](https://icode.best/i/47197145210746)  [link](https://blog.csdn.net/weixin_36002881/article/details/113236404) |

```shell
MySQL服务宕机了 （因为已经重启 所以排除）
Mysql的链接进程被主动kill掉。（这个也排除了 因为无人操作）
Mysql 链接超时 ，在某个mysql长连接的很久没有新的请求，达到了server端的timeout，被server强行关闭，此后再通过这个connection发起查询时，就会报错 server has gone away。（经过排查是属于这种）
```

> 就是说mysql 8个小时没有数据请求就自动关闭了连接,而队列由于常驻进程所以一直采用的是上一次的连接导致无法连接成功

```shell
show global variables like '%timeout';  # 查看连接超时命令
```



![image-20220820112512031](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220820112512031.png)

**解决方案**

> 找到**项目目录\config\database.php**

```php
....
'break_reconnect' => true,  // 是否需要断线重连 修改为true
...
```

### 模拟 MySQL server has gone away

**windows模拟**

>  进程启动的过程中 没有开启thinkphp5的断线重连(修改为true)
>
> **此电脑右键--管理(下面图中的标记打错字了)--服务和应用程序--服务(找到mysql服务右键重启)**

![image-20220820135548878](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220820135548878.png)

> 重启之后就可以看见**正在执行的队列报错**

![image-20220820140308715](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220820140308715.png)