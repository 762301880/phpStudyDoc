## **基本查询日期实战**

```php
return UsersModel::where(function ($query) use ($data) {
            $user_name = !empty($data['user_name']) ? $data['user_name'] : "";
            $nick_name = !empty($data['nick_name']) ? $data['nick_name'] : "";
            $phone = !empty($data['phone']) ? $data['phone'] : "";
            $start_time = !empty($data['start_time']) ? $data['start_time'] : "";
            $end_time = !empty($data['end_time']) ? $data['end_time'] : "";


            if (!empty($user_name)) {
                $query->where('user_name', 'like', "%$user_name%");
            }
            if (!empty($nick_name)) {
                $query->where('nick_name', 'like', "%$nick_name%");
            }
            if (!empty($phone)) {
                $query->where('phone', 'like', "%$phone%");
            }
            # 查询日期区间-因为tp5保存的都是时间戳类型 
            if (!empty($start_time) && !empty($end_time)) {
                $start_time = strtotime($start_time);
                $end_time = strtotime($end_time) + (86400 - 1); //时间查询构建最低时间+59小时59分钟59秒
                $query->where('create_time', 'between', [$start_time, $end_time]);
            }
        });
```

## 携带关联数据并筛选字段

```php
    public function getAuntComment($data)
    {
        $limit = !empty($data['limit']) ? $data['limit'] : 10;
        $list = AuntModel::where(['site_id' => $data['siteid'], 'delete_time' => 0])
            ->with(['auntRanking' => function ($model) {
                $model->field('*'); # 这里为什么这样写 为了以后约束字段 如果需要约束需要将 * 替换为需要关联的字段
            }])
            ->field('id,name')->paginate($limit);
        return $list;
    }
# f
     "data": [
            {
                "id": 100,
                "name": "哈啦少",
                "aunt_ranking": []
            },
            {
                "id": 104,
                "name": "扁鹊3333",
                "aunt_ranking": [
                    {
                        "id": 81,
                        "ranking": 0,
                        "create_time": 1645252831,
                        "remark": "212121",
                        "spec_id_string": "180,28",
                        "spec_name_string": "56天,砖石",
                        "name": "21",
                        "aunt_id": 104,
                        "admin_id": 1
                    }
                   ];
```

## 按照年龄区间查询(兼容多年龄)

![1646904210(1).jpg](https://s2.loli.net/2022/03/10/kPRxuC6FBN4vLyX.png) ![1646904274(1).jpg](https://s2.loli.net/2022/03/10/sC7UeYmMfJrDVPX.png)

> 遇到这么一个坑需求倒是不难是可以筛选不同的年龄段, 只要点了**不限** 不管点别的什么都是返回全部年龄段
>
> 反之返回符合条件的年龄段

```php
// 刚开始想的处理方法-结果发现并不能h
if (!empty($data['age_id'])){
    $ageIds=explode(',',$data['age_id']); // 0,1,2,3,4,5
    # 如果是有不限年龄查询直接返回空查询
    if (in_array(1,$ageIds)){
        $firstData=strtotime(date('Y-m-d',strtotime("-35 year")));
        $endData=strtotime(date('Y-m-d'));
        //$where[]=['aunt_basics.birth','LT',$data]; //35岁以下
        $where[]=['aunt_basics.birth','between',[$firstData,$endData]];  //46-50岁

    }
    if (in_array(2,$ageIds)){
        $firstData=strtotime(date('Y-m-d',strtotime("-36 year")));
        $endData=strtotime(date('Y-m-d',strtotime("-40 year")));
        $where[]=['aunt_basics.birth','between',[$firstData,$endData]];  //36-40岁
    }
    if (in_array(3,$ageIds)){
        $firstData=strtotime(date('Y-m-d',strtotime("-41 year")));
        $endData=strtotime(date('Y-m-d',strtotime("-45 year")));
        $where[]=['aunt_basics.birth','between',[$firstData,$endData]];  //41-45岁
    }
    if (in_array(4,$ageIds)){
        $firstData=strtotime(date('Y-m-d',strtotime("-46 year")));
        $endData=strtotime(date('Y-m-d',strtotime("-50 year")));
        $where[]=['aunt_basics.birth','between',[$firstData,$endData]];  //46-50岁
    }
    if (in_array(5,$ageIds)){
        $data=strtotime(date('Y-m-d',strtotime("-50 year")));
        $where[]=['aunt_basics.birth','ELT',$data]; // 50岁以上
    }
}





# 最终解决版本
# 因为数据库中的birth 保存为时间戳格式 所以需要转化,又有一点1970年之后无法转换年龄所以这里特殊处理
/**
 * 优化 使用IF函数处理年龄为空的情况
 * $age = "IF(pxs_aunt_basics.birth IS NULL,0,(DATE_FORMAT(NOW(), '%Y') - (DATE_FORMAT(DATE_ADD(FROM_UNIXTIME(0), INTERVAL  *             pxs_aunt_basics.birth SECOND),'%Y'))))";
 */
$age = "(DATE_FORMAT(NOW(), '%Y') - (DATE_FORMAT(DATE_ADD(FROM_UNIXTIME(0), INTERVAL pxs_aunt_basics.birth SECOND),'%Y')))";
        $res = AuntModel::leftJoin('pxs_aunt_basics', 'pxs_aunt_basics.aunt_id=pxs_aunt.id')
            ->field('pxs_aunt.id,pxs_aunt.name,pxs_aunt.grade_name,pxs_aunt.grade_id,pxs_aunt.pj_id,'
                .
                "pxs_aunt_basics.img,pxs_aunt_basics.education_id,pxs_aunt_basics.waiter,pxs_aunt_basics.birth_place,
            pxs_aunt_basics.birth,
            $age as age,
            pxs_aunt_basics.experience"
            )
            ->whereOr(function ($query) use ($age, $data) {
                $ageIds = explode(',', $data['age_id']); // 0,1,2,3,4,5   前端传输过来的是数字类型 对应不同的年龄段
                if (!empty($data['age_id'])) {
                    # 如果是有不限年龄查询直接返回空查询
                    if (in_array(1, $ageIds)&&!in_array(0,$ageIds)) {
                        $query->whereOr("$age<35");
                    }
                    if (in_array(2, $ageIds)&&!in_array(0,$ageIds)) {
                        $query->whereOr("$age between 36 and 40");
                    }
                    if (in_array(3, $ageIds)&&!in_array(0,$ageIds)) {
                        $query->whereOr("$age between 41 and 45");
                    }
                    if (in_array(4, $ageIds)&&!in_array(0,$ageIds)) {
                        $query->whereOr("$age between 46 and 50");
                    }
                    if (in_array(5, $ageIds)&&!in_array(0,$ageIds)) {
                        $query->whereOr("$age>50");
                    }
                }
            })
            # 这里筛选为什么要放在之后,因为orWhere 执行是顺序执行的 如果先执行了这里的语句 例如第一条
            # 所有不为零的阿姨那么之后再查orwhere 只能查询出不包含在where 条件之后的查询条件结果，中的结果
            ->where('pxs_aunt.delete_time', 'eq', 0)  
            ->where('pxs_aunt_basics.id', 'neq', null) 
            ->where($param['where'])//这里是旧的查询日后还要修改
            ->order($param['order'])
            ->select();
```

##  随机获取两条数据

> 利用mysql 的**order by Rand()** 方法随机排序，在加上**limit**约束条数即可实现需求

**参考资料**

| 名称           | 地址                                                   |
| -------------- | ------------------------------------------------------ |
| 第三方博客参考 | [link](https://www.cnblogs.com/fps2tao/p/9041204.html) |

**代码示例**

> tp可以利用**orderRand()**方法实现需求

```php
 /**
     * 返回随机的文章
     * @param int $article_id 当前的文章id为了不予当前的时间重合
     * @param int $num 需要展示的条数
     */
    protected function getRandArticle($article_id, $num)
    {
        if (empty($article_id)) return [];
        return ArticleModel::where('id', 'NEQ', $article_id)
            ->orderRand()
            ->limit($num)
            ->field(['id', 'title', 'img', 'view_count', 'is_recommend', 'create_time', 'update_time', 'status', 'seo_des', 'seo_keyword', 'seo_title'])
            ->select();
    }
```

## 多合一字段查询

```php
 if (!empty($text)) $query->where("CONCAT(字段一,字段二,字段三,字段N) like '%$text%'");
```

## 统一修改自动过滤数据库不存在的字段信息

> 在添加数据时往往不能确定接收的数据完全一一对应数据库字段的数据
> 接收的数据可能会有很多,且包括不是数据库字段的数据
> 这个时候就需要让程序自动过滤下非数据库字段的数据
>
> 在 TP5 中 提供了一个DB类 `strict(false)`可以自动过滤非数据库字段的数据

### 参考资料

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 技术博客 | [link](https://www.csdn.net/tags/NtDaIg5sNTc3MDUtYmxvZwO0O0OO0O0O.html) |

### 代码示例

```php
$order_edit_json_arr = json_decode($order_apply_refund->order_edit_json, true)?? []; //还原字段
$isUpdateOrder = OrderModel::where('id', $orderId)->strict(false)->update($order_edit_json_arr);
```

## [获取器](https://www.kancloud.cn/manual/thinkphp5_1/354046)

> 开发过程中我们经常需要对模型中的某个字段进行**特殊处理**,例如改变或序列化某个值的结果

```php
# 模型中定义获取器
/**
 * value 就是status 这个值本身,data整个模型的数据
 */
public function getStatusTextAttr($value,$data)
    {
        $status = [-1=>'删除',0=>'禁用',1=>'正常',2=>'待审核'];
        return $status[$data['status']];
    }
```

**实战示例**

> **说明** 这里有一个**坑**就是<font color='red'>如果需要调用别的变量中的方法需要赛选字段的时候带入别的变量</font>

```shell

# 代码调用
   public function queryOrderNumber($order_number)
    {
        $orderModelQuery = OrderModel::where('order_number', 'like', "%{$order_number}%");
        $list = $orderModelQuery->field([
            "id", "order_number", "service_id", "service_name",
            "user_name", "phone", "to_door_address", "city_path_id"
        ])->find();

        if (empty($list)) throw new SystemException("订单编号或者订单为空");
        return $list;
    }
# 模型中的获取器
    public function getCityPathIdTextAttr()
    {
        $city_path_id = $this->city_path_id ?? "";
        if (empty($city_path_id)) return "";
        return AddressModel::getInstance()->getCityPathName($city_path_id, '');
    }

    /**
     * 返回序列化之后的详细地址(包括保洁|保姆提交后的具体地址)
     * @return mixed|string
     */
    public function getToDoorAddressAttr($value)
    {
        $city_path_id_text = $this->getCityPathIdTextAttr() ?? "";
        if (empty($city_path_id_text)) return $value;
        return $city_path_id_text . '' . $value;
    }
```

## thinkphp 插入批量数据

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/tongkongyu/article/details/119939190) |

**代码示例**

```shell
  $data=[['aunt_id'=>123,'service_id'=>456],['aunt_id'=>123,'service_id'=>456]];
  $auntServiceModel= new  AuntServiceModel();
  dd($auntServiceModel->saveAll($data));
```

## 关联查询如何设置别名&&（日后统一的后台列表&详情查询）

### **对应数据表**

> 预约服务表

![image-20220715150216733](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220715150216733.png)

> 预约服阿姨表

![image-20220715150232580](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220715150232580.png)

### 需求

**列表页**

![image-20220715150409832](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220715150409832.png)

**详情页**

![image-20220715150444325](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220715150444325.png)



### **实战代码**

```shell
public function getOrderReserveListOrDetails($data)
    {
        $id = $data['id'] ?? "";
        $export = $data['export'] ?? "";
        $limit = !empty($data['limit']) ? $data['limit'] : 10;
        if (!empty($export) && $export == true) $limit = $this->getOrderServiceReserveQuery($data)::count() + 1;//因为下标从0开始导出所以要+1
        # 返回详情
        if (!empty($id)) {
            $details = $this->getOrderServiceReserveQuery($data)->with('orderReserveAunts')->find();
            $this->appendRetOrderReserveListOrDetailsText($details);
            return $details;
        }
        # 返回列表
        //getLastSql();
        $list = $this->getOrderServiceReserveQuery($data)
            ->order('id', 'DESC')
            ->field("reserve.*, ifnull(aunts.aunt_name,'-') as input_aunt_name ,aunts.id as aunts_id")
            ->paginate($limit);
        $list->getCollection()->map(function ($list) {
            $this->appendRetOrderReserveListOrDetailsText($list);
            return $list;
        });
        PaginateService::appendBackstageResponseData($list);
        return $list;
    }

    /**
     * 添加扩展字段返回
     * @param $data
     */
    protected function appendRetOrderReserveListOrDetailsText(OrderServiceReserveModel $reserveModel)
    {
        if (empty($reserveModel)) return [];
        $reserveModel->serialize_time_interval = $reserveModel->start_time . '~' . $reserveModel->end_time;
    }

    /**
     * 公用查询
     * @param $data
     */
    public function getOrderServiceReserveQuery($data)
    {
        $query = OrderServiceReserveModel::alias("reserve")
            ->where(function ($query) use ($data) {
                # 查询条件
                $reservation_number = $data['reservation_number'] ?? "";
                $id = $data['id'] ?? "";
                $user_name = $data['user_name'] ?? "";
                $user_phone = $data['user_phone'] ?? "";
                $aunt_name = $data['aunt_name'] ?? "";
                $service_id = $data['service_id'] ?? "";
                $start_time = $data['start_time'] ?? "";
                $end_time = $data['end_time'] ?? "";
                $reserve_start_time = $data['reserve_start_time'] ?? "";
                $end_start_time = $data['end_start_time'] ?? "";
                # 筛选
                if (!empty($reservation_number)) $query->where('reserve.reservation_number', 'like', "%{$reservation_number}%");
                if (!empty($id)) $query->where('reserve.id', $id);
                if (!empty($user_name)) $query->where('reserve.user_name', 'like', "%{$user_name}%");
                if (!empty($user_phone)) $query->where('reserve.user_phone', 'like', "%{$user_phone}%");
                if (!empty($service_id)) $query->where('reserve.service_id', $service_id);
                if (!empty($aunt_name)) $query->where('aunts.aunt_name', 'like', "%{$aunt_name}%");
                //日期查询
                if (!empty($start_time) && !empty($end_time)) $query->whereBetween('reserve.create_time', [$start_time . ' 00:00:00', $end_time . ' 23:59:59']);
                if (!empty($reserve_start_time) && !empty($end_start_time)) $query->whereBetween('reserve.reservation_time', [$reserve_start_time, $end_start_time]);
            });
        # 不是详情的话添加关联查询
        if (empty($data['id'])) {
            $noConditionOne = OrderReserveAuntModel::STATUS_INAPPROPRIATE; //排除条件1
            $noConditionTwo = OrderReserveAuntModel::STATUS_SCHEDULE_CONFLICT; //查询条件二
            $query->leftJoin('pxs_order_reserve_aunts aunts', "aunts.order_service_reserve_id=reserve.id and aunts.status != {$noConditionOne} and aunts.status !={$noConditionTwo}");
        }
        return $query;
    }
    
# 列表调用
    public function index(Request $request)
    {
        $data = $request->only([
            'page', 'limit', 'reservation_number', 'id', 'user_name', 'user_phone',
            'aunt_name', 'service_id', 'start_time', 'end_time',
            'reserve_start_time', 'end_start_time', 'export'
        ]);
        $res = $this->orderReserveService->getOrderReserveListOrDetails($data);
        //导出列表
        if (!empty($data['export']) && $data['export'] == true) {
            $data = $res['list'];
            return OrderServiceReserveModel::exportOrderServiceReserve($data);
        }
        $retText = !empty($id) ? "详情" : "列表";
        return $this->resSuccess($res, "服务订单{$retText}返回成功");
    }
```

## 优化相关

**参考资料**

| 名称 | 地址                                    |
| ---- | --------------------------------------- |
| 博客 | [link](https://blog.thinkphp.cn/843679) |

### 批量插入前端不要在循环体中重复查询数据库

> 例如以下代码 注释的位置是我采用**foreach**循环处理数据,经过本地测试循环100遍会有十几秒的处理时间,
>
> 所以我们将不必要的数据库查询(**业务逻辑一致的条件**)抽离出来,生成预约编号不是一样的所以不能抽离,经
>
> 测试抽离后的代码一次性插入执行只要三秒,主要是生成预约编号消耗了大量的时间,如果再抽离预约编号只需要
>
> 不到500毫秒

**参考资料**

| 名称 | 地址                                        |
| ---- | ------------------------------------------- |
| 博客 | [link](https://icode.best/i/90833040058234) |

**原始逻辑**

```php
        Db::startTrans();
        foreach ($reserve_times as $element) {
            $reservation_time = $element['reservation_time'] ?? "";
            $start_time = $element['start_time'] ?? "";
            $end_time = $element['end_time'] ?? "";
            $auntModel = AuntModel::get($aunt_id);
            $orderServiceReserve = OrderServiceReserveModel::updateOrCreate(['id' => $id]);
            if (!$id) $orderServiceReserve->reservation_number = OrderServiceReserveModel::generatereServationNumber();//生成预约编号
            $orderModel = OrderModel::where('id', $order_id)->find();
            $orderServiceReserve->user_name = $orderModel->user_name ?? "";
            $orderServiceReserve->order_id = $order_id;
            $orderServiceReserve->user_phone = $orderModel->phone ?? "";
            $orderServiceReserve->service_name = $orderModel->service_name ?? "";
            $orderServiceReserve->service_id = $orderModel->service_id ?? 0;
            $orderServiceReserve->to_door_address = $orderModel->to_door_address ?? "";
            $orderServiceReserve->reservation_time = $reservation_time;
            $orderServiceReserve->start_time = $start_time;
            $orderServiceReserve->end_time = $end_time;
            $orderServiceReserve->hours = $orderModel->hours ?? 0;
            $orderServiceReserve->aunt_name = $auntModel->name ?? "";
            $orderServiceReserve->aunt_id = $auntModel->id ?? 0;
            $orderServiceReserve->status = OrderServiceReserveModel::STATUS_STAY_SERVICE;
            $orderServiceReserve->is_fixed_aunt = $is_fixed_aunt;
            $orderServiceReserve->residence_area = $orderModel->residence_area ?? "";
            $orderServiceReserve->remark = ""; //特殊需求默认为空
            if ($orderServiceReserve->save()) {
                Log::info("小程序预约记录:预约成功,用户:" . $orderModel->user_name . "预约成功,订单id:" . $order_id . ",预约时间:" . date('Y-m-d H:i:s'));
                Queue::push(SendOrderReserveNoticeJob::class, ['order_service_reserve_id' => $orderServiceReserve->id]);//推送模板消息
                continue;
            }
            Db::rollback();
            return false;
        }
        Db::commit();
        return true;
```

**优化后的逻辑**

```php
 $reserve_times = $data['reserve_times'] ?? "";
        $order_id = $data['order_id'] ?? "";
        $is_fixed_aunt = $data['is_fixed_aunt'] ?? "";
        $aunt_id = $data['aunt_id'] ?? "";
        $id = $data['id'] ?? "";

        $auntModel = AuntModel::get($aunt_id);
        $orderModel = OrderModel::where('id', $order_id)->find();
        $data = array_map(function ($list) use ($auntModel, $order_id, $is_fixed_aunt, $orderModel) {
            unset($list['serialize_time_text']);
            $list['reservation_number'] = OrderServiceReserveModel::generatereServationNumber();//生成预约编号
            $list['user_name'] = $orderModel->user_name ?? "";
            $list['order_id'] = $order_id;
            $list['user_phone'] = $orderModel->phone ?? "";
            $list['service_name'] = $orderModel->service_name ?? "";
            $list['service_id'] = $orderModel->service_id ?? 0;
            $list['to_door_address'] = $orderModel->to_door_address ?? "";
            $list['hours'] = $orderModel->hours ?? 0;
            $list['aunt_name'] = $auntModel->name ?? "";
            $list['aunt_id'] = $auntModel->id ?? 0;
            $list['status'] = OrderServiceReserveModel::STATUS_STAY_SERVICE;
            $list['is_fixed_aunt'] = $is_fixed_aunt;
            $list['residence_area'] = $orderModel->residence_area ?? "";
            $list['remark'] = ""; //特殊需求默认为空
            return $list;
        }, $reserve_times);
        Db::startTrans();
        $orderServiceReserve = new OrderServiceReserveModel();
        $collections=$orderServiceReserve->saveAll($data);
        $ids=$collections->column('id');
        if (!$orderServiceReserve->saveAll($data)) {
            Db::rollback();
            return false;
        }
        Db::commit();
        return true;
```

**生成预约编号**

```php
    /**
     * 规则RS+年月日+000000~999999；
     * 生成预约编号
     */
    public static function generatereServationNumber()
    {
        $serialNumber = 'RS' . date('Ymd') . rand(000000, 999999);
        //避免重复生成 去数据库再次查询一遍
        if (self::whereIn('reservation_number', (array)$serialNumber)->find() != null) {
            return self::generatereServationNumber();
        }
        return $serialNumber;
    }
```

## where 条件日期转化查询

```php
 $spellGroupUsersModels = SpellGroupUsersModel::whereRaw("unix_timestamp(end_time) <= $thisTime")       # 用原始查询并转化
            ->where('status', 'EQ', SpellGroupUsersModel::STATUS_IN_SPELL_GROUP)
            ->where('parent_id', SpellGroupUsersModel::PARENT_ID_DEFAULT)
            ->select();
```

## 查询json数据

https://blog.csdn.net/grey_bear/article/details/122584902      json字段提取

[![xVh86s.png](https://s1.ax1x.com/2022/09/26/xVh86s.png)

```shell
	 getLastSql();
     //https://www.kancloud.cn/manual/thinkphp5_1/507621
     $stu = Stu::where('info->sex', 2)->select();
     dd($stu);
```

[**解决json为int类型无法查询问题**](https://www.kancloud.cn/manual/thinkphp5_1/507621)

> 需要指定设置对应json数据类型

```shell
// 设置json类型字段
    protected $json = ['info'];

    // 设置JSON字段的类型
    protected $jsonType = [
        'info->sex'	=>	'int'
    ];
```

### 选择json字段作为一级字段

```php
# 代码示例
  getLastSql();
        //https://www.kancloud.cn/manual/thinkphp5_1/507621
        $stu = Stu::field(["id","json_extract(info,'$.sex') as sex"])->where('info->sex', 2)->select();
        #or
        $stu = Stu::field(["id",Db::raw("json_extract(info,'$.sex') as sex ")])->where('info->sex', 2)->select();
        dd($stu->toArray());
# ---------------------------------------------
# 结果示例
^ array:7 [▼
  0 => array:2 [▼
    "id" => 1
    "sex" => "2"
  ]
  1 => array:2 [▼
    "id" => 2
    "sex" => "2"
  ]
  2 => array:2 [▶]
  3 => array:2 [▶]
  4 => array:2 [▶]
  5 => array:2 [▶]
  6 => array:2 [▶]
]
```

### 多字段json查询

https://blog.csdn.net/robinson_911/article/details/120370772?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-120370772-blog-119889086.pcrelevantt0_20220926_downloadratepraise_v1&spm=1001.2101.3001.4242.1&utm_relevant_index=2

![xZZ1ds.png](https://s1.ax1x.com/2022/09/27/xZZ1ds.png)

**代码示例**

```php
   $stu = Stu::where('info->article->content', 'like',"%4WHpEP050XLJ734%")->select();
   dd($stu->toArray());

 # 结果示例
^ array:1 [▼
  0 => array:7 [▼
    "id" => 10
    "sname" => "刘雷"
    "class_id" => null
    "birthday" => "1996-11-08 20:33:13"
    "updated_at" => "2022-09-27 08:45:38"
    "sex" => "男"
    "info" => {#56 ▼
      +"sex": 2
      +"name": "4Ij"
      +"score": 3
      +"article": {#58 ▼
        +"title": "DtpM2Wx5AI"
        +"content": "XwxBgzF7O9T3kah6J53x4WHpEP050XLJ734uuwufpLkv57dg29Np0n0Qb75IQF4Ab9K0WePpR1ojOckaMOxwJX05hL"
      }
    }
  ]
]
# ---------------------选择多级字段作为一级字段------------------------------    
      # 选择文章内容字段
      $stu = Stu::field("json_extract(info,'$.article.content') as content")->where('info->article->content', 'like',"%4WHpEP050XLJ734%")->select();

# 结果示例
^ array:1 [▼
  0 => array:1 [▼
    "content" => ""XwxBgzF7O9T3kah6J53x4WHpEP050XLJ734uuwufpLkv57dg29Np0n0Qb75IQF4Ab9K0WePpR1ojOckaMOxwJX05hL""
  ]
]
```

### **扩展补充**

### json字段查询取消双引号

> 采用**json_unquote**函数取消双引号,[参考](http://t.zoukankan.com/xiaomaomao-p-15630256.html)

```php
       $stu = Stu::field("json_unquote(json_extract(info,'$.article.content')) as content")
            ->where('info->article->content', 'like',"%4WHpEP050XLJ734%")
            ->select();
        dd($stu->toArray());
```

##  [thinkphp链式操作会保留上一个变量查询出来的值](http://www.360doc.com/content/21/0720/15/65839659_987450259.shtml)

```php
->removeOption()        # 使用removeOption链式移除查询条件,例如  ->removeOption("where")   弊端会直接移除所有的条件不建议使用  
    
    
        # 建议可以封装为这种不会保存上一步查询的条件
        $auntLeaveQuery = function ($aunt_id) use ($month_all_dates) { //公用查询
            return AuntLeaveModel::where('aunt_id', $aunt_id)->whereIn('date', $month_all_dates); //公用查询
        };

        $retData['leave_days'] = $auntLeaveQuery($aunt_id)->where('type', AuntLeaveModel::TYPE_LEAVE)->count() * $once_date; //请假天数
        $retData['rest_days'] = $auntLeaveQuery($aunt_id)->where('type', AuntLeaveModel::TYPE_REST)->count() * $once_date; //休息天数    
```

