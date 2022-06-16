

# 说明&资料

## 说明

> mysql超级用户下有很多特殊的数据库,例如**mysql** 里面存放着用户的信息，我们不希望开发人员可以看到这个库，
>
> 怕他误删，或者不想给开发人员超级用户账户信息，又或者只想给他设置一个只读数据库，这时候就可以给他设置一个
>
> 新账号并设置权限

## 资料

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 第三方博客             | [link](https://blog.csdn.net/weixin_30516835/article/details/113191074?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-113191074-blog-111882700.pc_relevant_antiscanv3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7EBlogCommendFromBaidu%7ERate-1-113191074-blog-111882700.pc_relevant_antiscanv3&utm_relevant_index=2) |
| mysql数据库的grant授权 | [菜鸟教程-MySQL Grant 命令](https://www.runoob.com/note/19873)   [百度文库](https://wenku.baidu.com/view/51ef763a5aeef8c75fbfc77da26925c52cc591e2.html) |

## mysql5.7 创建用户并修改权限 

```php
 create  user  '用户名'@'ip地址'  identified by  '密码';
 grant select, insert, update, delete on 数据库名称.* to 用户名@'ip地址'; # 设置所有权限
```

