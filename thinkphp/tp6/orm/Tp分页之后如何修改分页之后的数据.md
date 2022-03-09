# 说明

> 采用***$printersModel->getCollection()***获取分页之后的数据，然后采用**map()**
>
>方法修改原有的值

# 逻辑代码示例

```php
<?php

namespace app\admin\service;

use app\common\model\UsersModel;

class UserService
{
    public function getUserList($data)
    {
        $limit = !empty($data['limit']) ? $data['limit'] : 10;
        $list = $this->getUserQuery($data)->paginate($limit)->each(function ($list) { # c
           $list->header_img=$list->getHeaderImg(); //处理图片
           $list->sex=$list->getSex();
        });
        return $list;
    }

    public function getUserQuery($data)
    {

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
            if (!empty($start_time) && !empty($end_time)) {
                $start_time = strtotime($start_time);
                $end_time = strtotime($end_time) + (86400 - 1); //时间查询构建最低时间+59小时59分钟59秒
                $query->where('create_time', 'between', [$start_time, $end_time]);
            }
        })
            ->order('id', 'DESC');
    }

}
```

