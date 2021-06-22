# 一、说明

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

## 2.1数据库消息通知

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
  `type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `notifiable_type` varchar(255) COLLATE utf8mb4_unicode_ci NOT NULL,
  `notifiable_id` bigint(20) unsigned NOT NULL,
  `data` text COLLATE utf8mb4_unicode_ci NOT NULL,
  `read_at` timestamp NULL DEFAULT NULL,
  `created_at` timestamp NULL DEFAULT NULL,
  `updated_at` timestamp NULL DEFAULT NULL,
  PRIMARY KEY (`id`),
  KEY `notifications_notifiable_type_notifiable_id_index` (`notifiable_type`,`notifiable_id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```



