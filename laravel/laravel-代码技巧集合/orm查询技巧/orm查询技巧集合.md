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

#  [创建&新增数据](https://learnku.com/docs/laravel/8.x/eloquent/9406)

> 利用`laravel`  `Eloqument Orm 快速入门` 中的`firstOrNew`  或者 ``updateOrCreate``  可以实现

**firstOrNew示例**

```php
 /**
     * 创建或新增导航
     * @param $data
     * @return mixed
     */
    public function createOrUpdateNavigationModel($data)
    {
        $id = $data['id'] ?? 0; //主键数据库那边要设置自动递增
        $sort = $data['sort'] ?? NavigationModel::SORT_DEFAULT;
        $title = $data['title'] ?? "";
        $transform_title = $data['transform_title'] ?? "";
        $parent_id = $data['parent_id'] ?? NavigationModel::PARENT_ID_DEFAULT;
        $icon = $data['icon'] ?? "";
        $link_url = $data['link_url'] ?? "";
        $navigationModel = NavigationModel::firstOrNew(['id' => $id]);
        $navigationModel->id = $id;
        $navigationModel->sort = $sort;
        $navigationModel->title = $title;
        $navigationModel->transform_title = $transform_title;
        $navigationModel->parent_id = $parent_id;
        $navigationModel->icon = $icon;
        $navigationModel->link_url = $link_url;
        return $navigationModel->save();
    }
```

## json查询

**数据示例**

[![xVh86s.png](https://s1.ax1x.com/2022/09/26/xVh86s.png)](https://imgse.com/i/xVh86s)

```mysql
CREATE TABLE `stu` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `sname` char(10) DEFAULT NULL,
  `class_id` int(11) DEFAULT NULL,
  `birthday` datetime DEFAULT NULL,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `sex` enum('男','女') DEFAULT NULL,
  `info` json DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='学生表';
```



**代码示例**

```php
   public function test(Request $request)
    {
//        $stus = Stu::all();
//
//        foreach ($stus as $element) {
//            $ranName = Str::random(3);
//            $ranSex = rand(1, 2);
//            $ranScore = random_int(1, 100);
//            $element->info = json_encode([
//                'name' => $ranName,
//                'sex' => $ranSex,
//                'score' => $ranScore
//            ]);
//            $element->save();
//        };

        $stu=Stu::where("info->sex",1)->get();
        dd($stu->toArray());
    }

# 结果示例

array:3 [▼
  0 => array:8 [▼
    "id" => 5
    "sname" => "后盾人"
    "class_id" => 2
    "birthday" => "2003-09-01 20:33:13"
    "updated_at" => "2022-09-26T09:29:44.000000Z"
    "sex" => "男"
    "info" => "{"sex": 1, "name": "klI", "score": 40}"
    "sex_serialize_int" => 1
  ]
  1 => array:8 [▼
    "id" => 6
    "sname" => "张云"
    "class_id" => 3
    "birthday" => "1996-09-01 20:33:13"
    "updated_at" => "2022-09-26T09:29:44.000000Z"
    "sex" => "女"
    "info" => "{"sex": 1, "name": "dOE", "score": 62}"
    "sex_serialize_int" => 2
  ]
  2 => array:8 [▼
    "id" => 8
    "sname" => "李兰"
    "class_id" => 2
    "birthday" => null
    "updated_at" => "2022-09-26T09:29:44.000000Z"
    "sex" => "女"
    "info" => "{"sex": 1, "name": "Fim", "score": 76}"
    "sex_serialize_int" => 2
  ]
]
```

