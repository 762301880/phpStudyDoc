# 资料

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://juejin.cn/post/6855129006761574407#heading-9) [link](https://www.cnblogs.com/youxin/archive/2013/04/17/3025381.html) |

# 	一、说明

> 消息提醒类似于 如果有人评论你，或则点赞你然后会有一个消息列表里面记录着别人
>
> 对你评论或则点赞的提醒信息,`laravel`的消息通知不仅有邮件通知而且还有数据库通知

- 例子可以参考钉钉

![image-20210622164447771](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210622164447771.png)

- 资料

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| `laravel`消息通知手册 | [链接](https://learnku.com/docs/laravel/8.x/notifications/9396#c7aec6) |

# 二、消息提醒使用

## 2.1数据库消息通知说明

>database 通知频道会在数据库中存储通知信息。该表包含通知的类型和描述通知的自定义 `JSON `数据之类的信息。
>
>您可以将其从数据表中查询出来，并显示在用户界面中。不过，在此之前，您需要创建一个数据库来维持您的通知。
>
>您可以使用` notifications:table` 命令来生成一个包含相应的数据表的迁移

```php
# 生成迁移数据库 
php artisan notifications:table # 此命令会生成:`database\migrations\2021_06_22_163654_create_notifications_table.php`
php artisan migrate # 此迁移命令会在数据库生成对应的数据表
```

- 生成的数据结构如下

```sql
CREATE TABLE `notifications` (
  `id` char(36) COLLATE utf8mb4_unicode_ci NOT NULL,
  `type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL, # App\Notifications\UserLoginNotification  --保存来自那个通知
  `notifiable_type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL, # App\Models\User   --通知类
  `notifiable_id` bigint(20) unsigned NOT NULL,              -- 通知id
  `data` text COLLATE utf8mb4_unicode_ci NOT NULL,           -- 传入的数据
  `read_at` timestamp NULL DEFAULT NULL,                     -- 阅读时间
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `notifications_notifiable_type_notifiable_id_index` (`notifiable_type`,`notifiable_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

## 2.2 使用

> 假设我们需要用户登录的时候有一个通知谁谁谁登录了，

### 2.2.1 创建消息通知

- 使用`artisan`命令创建通知

> ` php artisan make:notification`+  你需要定义的通知名称 

```php
# 此命令会在以下目录中生成通知类 app\Notifications\UserLoginNotification.php
php artisan make:notification UserLoginNotification
```

- 在需要对应的实体类模型中引用通知

```php
# 在模型中引用Notifiable
class User extends Authenticatable
{
    use Notifiable;
    -----省略代码   
}   
```

- 控制器中代码

> 例如我需要在登录的时候通知

```php
 public function login(Request $request)
    {
        $validator = UserLoginValidator::validate($request);
        if ($validator->fails()) {
            return ResponseLayout::apply(false,$validator->errors()->messages());
        }
        $user=User::where(['email' => $request->email, 'password' => $request->password])->first();
        if ($user) {
            $user->api_token=Str::random(20);
            $user->save();
            $user->notify(new UserLoginNotification($user));# 此处就是引用实体类传参通知
            return ResponseLayout::apply(true,'登陆成功');
        } else {
            return ResponseLayout::apply(false,'登陆失败');
        }
    }
```

- 通知类代码

```php
    public $user;
    # 需要定义依赖注入接受参数
    public function __construct(User $user)
    {
        $this->user=$user;
    }
    # 开启数据库通知
    public function via($notifiable)
    {
        return ['database'];
    }
    # toDatabase 中保存数据库 $notifiable默认就是传递过来的实参
    public function toDatabase($notifiable)
    {
        #dd($notifiable instanceof User); 结果 true
        # 直接将结果序列化至数据库
       return [
           $notifiable
       ];
    }
```

## 2.3更多使用方法

```php
#标记通知已读
$user->unreadNotifications->markAsRead();#user==实体类
# 大规模设置已读
$user->unreadNotifications()->update(['read_at' => now()]);
#查询数量
$user->unreadNotifications()->count();
# 删除
$user->notifications()->delete();
```



# 三 手撕站内信(原生)

**参考资料**

| 名称                              | 地址                                          |
| --------------------------------- | --------------------------------------------- |
| 知乎(PHP实现站内信设计思路与方案) | [link](https://zhuanlan.zhihu.com/p/92383224) |

**理解**

> **站内信通常需要解决两个需求**
>
> 用户对用户的站内信，管理员对用户的站内信：即一对一发送(**点对点**)
>
> 管理员对多用户、用户组、全站的站内信：即一对多发送(**点到面**)

## 数据库设计

```sql
CREATE TABLE IF NOT EXISTS `message` (
  `id` int(11) NOT NULL AUTO_INCREMENT,  #站内信的唯一标识符，使用自增长int类型;
  `sender_id` int(11) NOT NULL,        # 站内信的发送者ID，使用int类型;
  `receiver_id` int(11) NOT NULL,       #站内信的接收者ID，使用int类型;可以加一个模型字段就可以对应出是那张表的发送者id
  `title` varchar(255) NOT NULL,      # 站内信的标题，使用varchar类型;
  `content` text NOT NULL,            # 站内信的正文，使用text类型;
  `created_at` datetime NOT NULL,    # 站内信的创建时间，使用datetime类型。   
  `is_read` tinyint(1) NOT NULL DEFAULT '0' COMMENT '0表示未读，1表示已读',  # (建议用时间字段表示read_at 未读表示空读了则显示时间)
  `read_at`   TIMESTAMP NULL DEFAULT NULL COMMENT '站内信阅读时间';
  PRIMARY KEY (`id`) 
) ENGINE=InnoDB AUTO_INCREMENT=1 DEFAULT CHARSET=utf8mb4;
```

## 逻辑示例

```php
// 发送站内信的业务逻辑
if ($_POST['action'] == 'send_message') {
  $sender_id = $_SESSION['user_id']; // 获取发送者ID
  $receiver_id = $_POST['receiver_id']; // 获取接收者ID
  $title = $_POST['title']; // 获取站内信标题
  $content = $_POST['content']; // 获取站内信正文
  $timestamp = date('Y-m-d H:i:s'); // 获取当前时间

  // 执行插入站内信操作
  $sql = "INSERT INTO message (sender_id, receiver_id, title, content, created_at) VALUES ('$sender_id', '$receiver_id', '$title', '$content', '$timestamp')";
  $result = $db->query($sql);

  // 返回发送站内信结果
  if ($result) {
    echo json_encode(array('success' => true));
  } else {
    echo json_encode(array('success' => false));
  }
  exit;
}

// 获取用户的站内信列表
$sql = "SELECT * FROM message WHERE receiver_id = {$_SESSION['user_id']} ORDER BY created_at DESC";
$result = $db->query($sql);

$messages = array();
if ($result && $result->num_rows > 0) {
  while ($row = $result->fetch_assoc()) {
    array_push($messages, $row);
  }
}

// 获取站内信详情
if ($_GET['action'] == 'message_detail' && intval($_GET['message_id']) > 0) {
  $sql = "SELECT * FROM message WHERE id = {$_GET['message_id']}";
  $result = $db->query($sql);

  if ($result && $result->num_rows > 0) {
    $message = $result->fetch_assoc();
  }
}

// 设置阅读时间
function mark_as_read($message_id) {
    // 在数据库中将read_at字段更新为当前时间戳
    $query = "UPDATE `message` SET `read_at` = NOW() WHERE `id` = $message_id";
    $result = mysqli_query($db, $query);
    return $result; // 返回更新结果
}
```





