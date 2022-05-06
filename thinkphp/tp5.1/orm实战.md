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

