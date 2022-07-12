#  查询的数据指定多少日期之后才显示

> 例如我想让用户第二天才可以看见第一天的中奖数据，数据库中保存的**data** 格式为***2021-10-25***这种类型
>
> 先转化为unix时间戳然后加上需要什么时候查看的时间直接<=当前日期即可

```shell
        $gainPrizeList = $this->gainPrizeList($request)
            ->when(!empty($request->input('date')) && $request->input('type') == PullNewGainPrizeModel::TYPE_FALSE, function ($query) {
                // 第一天导入的第二天 0:30 才显示
                $today = (strtotime(date('Y-m-d H:i:s')));
                $oneDay = 60 * 60 * 24;
                $expirationTime = $oneDay + 1800; //过期时间
                $query->whereRaw("UNIX_TIMESTAMP(date) + $expirationTime <= $today"); # 核心逻辑
            })
            ->paginate($request->input('limit', 999));
```

# [leftjoin多条件查询](https://www.jianshu.com/p/ed5af6051059)



# 自动添加不存在的字段

> 利用模型的访问器处理字段并添加到模型中

[资料](https://learnku.com/docs/laravel/8.x/eloquent-mutators/9409#defining-an-accessor)

**代码示例**

```shell
# 模型中添加访问器
    protected $appends = ['sex_serialize_int']; # 将访问器添加的字段添加到查询的模型中

    public function getSexSerializeIntAttribute()
    {
        $sex = $this->sex;
        $arr = ['男' => 1, '女' => 2, 'default' => ''];
        return $arr[$sex] ?? $arr['default'];
    }
```



**返回结果**

```shell
array:10 [▼
  0 => array:7 [▼
    "id" => 1
    "sname" => "李子"
    "class_id" => 1
    "birthday" => "1998-02-12 08:22:13"
    "updated_at" => "2022-06-07T00:49:15.000000Z"
    "sex" => "男"
    "sex_serialize_int" => 1
  ]
  1 => array:7 [▼
    "id" => 2
    "sname" => "何青"
    "class_id" => 1
    "birthday" => "1985-07-22 18:19:13"
    "updated_at" => "2019-07-17T13:50:38.000000Z"
    "sex" => "女"
    "sex_serialize_int" => 2
  ]
  2 => array:7 [▶]
  3 => array:7 [▶]
  4 => array:7 [▶]
  5 => array:7 [▶]
  6 => array:7 [▶]
  7 => array:7 [▶]
  8 => array:7 [▶]
  9 => array:7 [▼
    "id" => 10
    "sname" => "刘雷"
    "class_id" => null
    "birthday" => "1996-11-08 20:33:13"
    "updated_at" => "2019-07-20T07:59:28.000000Z"
    "sex" => null
    "sex_serialize_int" => ""
  ]
]
```

