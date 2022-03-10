**基本查询日期实战**

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

> 携带关联数据并筛选字段

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

