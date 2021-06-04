#  说明

- 思路

> 点赞就是用户对某一条动态(比如朋友圈的说说)进行一次点击事件，点击一次点赞表会生成一条自己的点赞记录,
>
> 再次点击取消点赞

# 数据库设计

- 动态表（文章表）

> 文章隶属于编写的用户

| 名称       | 类型      | 说明                 |
| ---------- | --------- | -------------------- |
| id         | int       | id主键               |
| user_id    | int       | 用户id，点赞的用户id |
| content    | varchar   | 文章内容             |
| created_at | timestamp | 创建时间             |
| updated_at | timestamp | 修改时间             |

- 用户表

| 名称       | 类型      | 说明     |
| ---------- | --------- | -------- |
| id         | int       | id主键   |
| nickname   | varchar   | 用户名称 |
| created_at | timestamp | 创建时间 |
| updated_at | timestamp | 修改时间 |

- 文章用户点赞表

  >
  >
  >多对多的关系，一个文章可以有多个用户点赞，多个用户也可以点赞一个文章

| 名称       | 类型      | 说明                 |
| ---------- | --------- | -------------------- |
| id         | int       | id主键               |
| user_id    | int       | 用户id，点赞的用户id |
| article_id | int       | 文章id(发布的动态id) |
| created_at | timestamp | 创建时间             |
| updated_at | timestamp | 修改时间             |

# 模型设计

## 用户表

[withTimestamps]https://learnku.com/docs/laravel/8.x/eloquent-relationships/9407）

```php
namespace App\Http\Models;
use Illuminate\Database\Eloquent\Model;
class User extends Model
{
    protected $table = 'user';
    #关联的动态表,多对多
    public function article()
    {
        return $this->belongsToMany(article::class, 'article_user', 'user_id', 'article_id');
    }
     #点赞或取消方法
    public function follwToggle($id)
    {
        return $this->article()->withTimestamps()->toggle($id)['attached'] == null ? 0 : 1;
    }
}
```

## 控制器调用方法

```php
  public function articleLike(Request $request)
    {
        $user = User::find($request->id);
        if (empty($user)) {
            return $this->error('用户不存在');
        }
        #调用用户表中的点赞方法传入需要点赞的用户id
        $is_like = $user->follwToggle($request->input('article_id'));
        return $is_like?$this->success($is_like,'点赞成功'):$this->error($is_like,'点赞失败');
    }
```

