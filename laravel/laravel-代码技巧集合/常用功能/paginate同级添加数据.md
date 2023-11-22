# 说明

> 开发的时候有的返回结果能放在最外层就放在最外层



## 参考资料

| 名称                                   | 地址                                                         |
| -------------------------------------- | ------------------------------------------------------------ |
| laravel paginate 增加属性 （data同级） | [link](https://blog.csdn.net/ljwaheng/article/details/89477047) |
| 增加同级                               | [link](https://www.it1352.com/1540344.html)                  |



```shell
       $inviteRecord = PullNewInviteRecord::where(['inviter_uuid' => $uuid])
            ->where('p_id', '!=', 0)
            ->latest()
            ->paginate($request->input('limit', 10));
        //添加一层paginate数据 判断是否是主动邀请人
        $append_data = collect(['is_invitee' => $this->isInvitee($uuid)]);
        $inviteRecord = $append_data->merge($inviteRecord);
        return $inviteRecord;
        # 返回结果示例
        {
    "code": "0000",
    "msg": "邀请好友列表返回成功",
    "data": {
        "is_invitee": true, # 可以看见新加的结果已经出了
        "current_page": 1,
        "data": [],
        "first_page_url": "http://www.zhixiaozi-main.com/api/v1/lottery_draw/invite_friend_list?page=1",
        "from": null,
        "last_page": 1,
        "last_page_url": "http://www.zhixiaozi-main.com/api/v1/lottery_draw/invite_friend_list?page=1",
        "next_page_url": null,
        "path": "http://www.zhixiaozi-main.com/api/v1/lottery_draw/invite_friend_list",
        "per_page": 999,
        "prev_page_url": null,
        "to": null,
        "total": 0
    }
}
```

