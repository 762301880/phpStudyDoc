# 一、参考资料

| name                                           | url                                                          |
| ---------------------------------------------- | ------------------------------------------------------------ |
| 第三方博客:无限极分类原理与实现                | [链接](https://www.jianshu.com/p/ff07b46666c7)               |
| 第三方博客:打造一个无限极评论模块              | [链接](https://juejin.cn/post/6844903475336183822)           |
| 第三方博客:php用户评论系统,php实现文章评论系统 | [链接](https://blog.csdn.net/weixin_30548963/article/details/115150125) |

## 1.2 数据库设计

> 我们需要两张表一张发布`post`表，一张评论`comment`表

- post`文章表`

| id   | user_id(用户id) | title(文章名称)                      | content(文章内容)      | created_at(创建时间) | updated_at(更新时间) |
| ---- | --------------- | ------------------------------------ | ---------------------- | -------------------- | -------------------- |
| 1    | 101             | 孙悟空为什么被压了五百年以后越来越弱 | 因为....此处省略五百字 | 2021-06-13 14：15    | 2021-06-13 14：15    |

- comment`评论表`

| id   | post_id(文章主键) | comment_user_id(被评论的用户id) | user_id(发布评论的用户id) | content(评论的内容)    | comment_id(被回复的评论id,父级默认为0) | created_at        | updated_at        |
| ---- | ----------------- | ------------------------------- | ------------------------- | ---------------------- | -------------------------------------- | ----------------- | ----------------- |
| 1    | 1                 | 101                             | 505                       | 说得好                 | 0                                      | 2021-06-13 14：15 | 2021-06-13 14：15 |
| 2    | 1                 | 505                             | 301                       | 你他娘的真是个人才     | 1                                      | 2021-06-13 14：15 | 2021-06-13 14：15 |
| 3    | 1                 | 301                             | 303                       | 快把我的意大利炮拉上来 | 2                                      | 2021-06-13 14：15 | 2021-06-13 14：15 |
|      |                   |                                 |                           |                        |                                        |                   |                   |



# 二、逻辑代码

## 2.1 核心代码设计

```php
# 参数1 评论表的id,参数2 被回复的评论id,参数3,数据可以为空 
public function show($post_id,$comment_id,&$result=array())
     $comment=$this->getComment($post_id,$comment_id,&$result=array());
     return $this->success($comment); 
   }
# 这里有个问题就是如果需要返回resful api 的时候返回的数据会有点凌乱,解决方案 创建一个回调函数专门用来处理数据即可
public function getComment($post_id,$comment_id,&$result=array()){
    {
        $results=Comments::where(['post_id'=>$post_id,'comment_id'=>$comment_id])->get();
        foreach ($results as $value){
            $thisArr=&$result[];
            $value['comment']=$this->show($value['post_id'],$value['id'],$thisArr);
        }
        return $results;
}
```

## 结果示例

```php
[
    {
        "id": 1,
        "comment_user_id": null,
        "post_id": 1,
        "user_id": 2,
        "content": "孙悟空学圆滑了",
        "comment_id": 0,
        "created_at": "2021-06-12T08:10:29.000000Z",
        "updated_at": "2021-06-12T08:10:29.000000Z",
        "comment": [
            {
                "id": 2,
                "comment_user_id": null,
                "post_id": 1,
                "user_id": 3,
                "content": "说得好",
                "comment_id": 1,
                "created_at": "2021-06-12T08:10:29.000000Z",
                "updated_at": "2021-06-12T08:10:29.000000Z",
                "comment": [
                    {
                        "id": 7,
                        "comment_user_id": null,
                        "post_id": 1,
                        "user_id": 8,
                        "content": "确实说得好",
                        "comment_id": 2,
                        "created_at": "2021-06-12T08:10:29.000000Z",
                        "updated_at": "2021-06-12T08:10:29.000000Z",
                        "comment": []
                    },
                    {
                        "id": 3,
                        "comment_user_id": null,
                        "post_id": 1,
                        "user_id": 3,
                        "content": "6",
                        "comment_id": 2,
                        "created_at": null,
                        "updated_at": null,
                        "comment": [
                            {
                                "id": 5,
                                "comment_user_id": 3,
                                "post_id": 1,
                                "user_id": 5,
                                "content": "现代生存准则",
                                "comment_id": 3,
                                "created_at": null,
                                "updated_at": null,
                                "comment": [
                                    {
                                        "id": 8,
                                        "comment_user_id": null,
                                        "post_id": 1,
                                        "user_id": 90,
                                        "content": "人生啊",
                                        "comment_id": 5,
                                        "created_at": null,
                                        "updated_at": null,
                                        "comment": []
                                    }
                                ]
                            }
                        ]
                    }
                ]
            }
        ]
    },
    {
        "id": 4,
        "comment_user_id": null,
        "post_id": 1,
        "user_id": 4,
        "content": "说白了还是怂怕被如来佛祖又压回去",
        "comment_id": 0,
        "created_at": null,
        "updated_at": null,
        "comment": []
    }
]
```



## 此处需要注意的是删除评论的时候 一级评论需要删除连带的评论

