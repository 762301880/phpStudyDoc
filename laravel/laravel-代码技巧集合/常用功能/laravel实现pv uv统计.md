# 一、说明

- 资料

| 资料名称                                                     | 资料地址                                      |
| ------------------------------------------------------------ | --------------------------------------------- |
| laravel学院-基于 Redis HyperLogLog 实现用户 UV 统计中间件功能 | [链接](https://laravelacademy.org/post/22189) |

- 说明

> `PV`（Page View）访问量, 即页面浏览量或点击量，衡量网站用户访问的网页数量；在一定统计周期内用户每打开或刷新一个页面就记录1次，多次打开或刷新同一页面则浏览量累计。
>
> UV（Unique Visitor）独立访客，统计1天内访问某站点的用户数(以cookie为依据);访问网站的一台电脑客户端为一个访客。可以理解成访问某网站的电脑的数量。网站判断来访电脑的身份是通过来访电脑的cookies实现的。如果更换了`IP`后但不清除cookies，再访问相同网站，该网站的统计中UV数是不变的。如果用户不保存cookies访问、清除了cookies或者更换设备访问，计数会加1。`00:00-24:00`内相同的客户端多次访问只计为1个访客。
>
> 简单来说就是`pv`统计单个页面的所有访问量加入一个用户进入了100次需要统计的页面也统计100此
>
> `uv`及用户进入100此此页面一天只统计一次

 ## 1.1实现 方案

> 采用`redis`的`incr()`自增方法统计`pv`,采用`redis`的`setbit()`方法实现统计`uv`
>
> 用户每次点击需要的页面的时候统计`pv&uv`，使用`linux`的`crontab`定时任务
>
> 实现每天将`reids`中的`pv&uv`保存到数据库中

# 二、实现逻辑

## 2.1 定义事件

- 定义`Statistical`统计事件

```php
    # 关键参数
    public $statistical_page_name;//统计页面名称
    public $user_id;//用户id
    public function __construct($statistical_page_name,$user_id)
    {
        $this->statistical_page_name = $statistical_page_name;
        $this->user_id=$user_id;
    }
```

- 定义`StatisticalPv`监听则统计`pv`

```php
 public function handle(Statistical $event)
    {

        $data=date('Ymd');//当前的日期精确到天
        Redis::incr('pv:'.$event->statistical_page_name.$data);#redis自增
    }
```

- 定义`StatisticalUv`监听则统计`uv`

```php
  public function handle(Statistical $event)
    {
        $data=date('Ymd');//当前的日期精确到天
        Redis::setbit('uv:'.$event->statistical_page_name.$data,$event->user_id,1);
    }
```

- 在`app\Providers\EventServiceProvider`中定义绑定的事件与监听者

```php
 protected $listen = [ 
   Statistical::class => [
            StatisticalPv::class,//pv统计
            StatisticalUv::class,//uv统计
        ],
     ]
```



- 然后写一个方法让前端调用触发事件

```php
 public function save(Request $request)
    {
        try {
            if ($request->input('statistical_page_name') == null) {
                throw new ApiException('统计页面名称不能为空');
            }
            event(new Statistical($request->input('statistical_page_name'), $this->user()->id));# 触发pv&uv统计事件
            return $this->success('','统计成功');
        } catch (ApiException $apiException) {
            return $this->error($apiException->getMessage());
        }
    }
```

## 2.2定时任务同步`mysql`

### 2.2.1数据库设计

```php
CREATE TABLE `club2_statistical` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `statistical_page_name` char(10) DEFAULT NULL COMMENT '统计页面名称',
  `pv` int(255) DEFAULT NULL COMMENT 'page view  计算所有的页面点击',
  `uv` int(255) DEFAULT NULL COMMENT 'user view 单个用户一天只计算一次',
  `statistical_time` varchar(255) DEFAULT NULL COMMENT '统计的时间日期',
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=254 DEFAULT CHARSET=utf8mb4 COMMENT='统计pv-uv表';
```

### 2.2.1主逻辑

- 先写一个逻辑接口

```php
 public function store()
    {
        $day_before_timestamp=strtotime(date('Y-m-d'))-86400;//前一天的时间戳
        $date = date('Ymd',$day_before_timestamp);//获取上一天的时间
        $redis = Redis::connection();
        # 这里有多少页面就写多少个
        #获取单个的
        # todo 此处应该获取多个值进行循环插入先需要判断取到的是否有值如果没有值给个默认值0,记得取完值之后删除redis表中不需要的数据
        $params = [0 => '本校动态列表', 1 => '发布动态', 2 => '投票页面', 3 => '篮球比赛页面', 4 => '简历活动首页',
            5 => '攻略页面', 6 => '简历修改意见页面', 7 => '职位详情', 8 => '资讯页面', 9 => '我的积分'
            , 10 => '推荐给好友'];
        foreach ($params as $value) {
            $pvCount = $redis->get('pv:' . $value . $date);//获取上一天的pv统计
            $uvCount = $redis->bitcount('uv:' . $value . $date);//获取上一天的uv统计
            $statistical = new Club2Statistical();
            $statistical->statistical_page_name = $value;
            $statistical->pv = $pvCount ?? 0;
            $statistical->uv = $uvCount ?? 0;
            $statistical->statistical_time = date('Y-m-d',$day_before_timestamp);//统计的时间日期默认上一天
            $isSave = $statistical->save();
            //保存之后删除redis中的旧数据
            if ($isSave) {
                $redis->del('pv:' . $value . $date);
                $redis->del('uv:' . $value . $date);
            }
        }
        return;
    }
```

- 在`linux`中创建定时任务

```php
crontab -e 
0 6 * * * curl +你的接口名称    #定义每天六点定时保存到数据库
```

## 2.3 后台取值

```php
  public function index(Request $request)
    {
        $club2Statistical = Club2Statistical::filter($request->all(), StatisticsFilter::class)->get()->groupBy('statistical_time');
        return $this->success($club2Statistical, '统计信息返回成功');
    }
```

- 查询条件`StatisticsFilter::class`

```php
<?php

namespace App\Http\ModelFilters;

use Carbon\Carbon;
use EloquentFilter\ModelFilter;

class StatisticsFilter extends ModelFilter
{
    /**
     * Related Models that have ModelFilters as well as the method on the ModelFilter
     * As [relationMethod => [input_key1, input_key2]].
     *
     * @var array
     */
    public $relations = [];

    public function statisticalPageName($statistical_page_name)
    {
        $this->where('statistical_page_name', $statistical_page_name);
    }

    public function setup()
    {
        //开始-结束时间
        if (!empty($this->input('start_time')) && !empty($this->input('end_time'))) {
            $this->whereBetween('statistical_time', [$this->input('start_time'), $this->input('end_time')]);
        }
        //如果选择的开始时间与结束时间为空则默认显示从现在开始七天前的时间
        if (empty($this->input('start_time')) && empty($this->input('end_time'))) {
            $seven_days = strtotime(date('Y-m-d', time())) - (7 * 86400);
            $seven_days = date('Y-m-d', $seven_days);
            $this->whereBetween('statistical_time', [$seven_days, date('Y-m-d', time())]);
        }
        if ($this->input('type') == 1) {
            $this->select('id', 'statistical_page_name', 'pv', 'statistical_time', 'created_at', 'updated_at');
        } elseif ($this->input('type') == 2) {
            $this->select('id', 'statistical_page_name', 'uv', 'statistical_time', 'created_at', 'updated_at');
        }
//        //最近多少天
//        if (!empty($this->input('day'))) {
//            $start_time=Carbon::now()->toDateTimeString();
//            dd($start_time);
//
//            $this->whereBetween('statistical_time', [$this->input('start_time'), $this->input('end_time')]);
//        }
        $this->orderBy('created_at', 'DESC');
    }
}

```

