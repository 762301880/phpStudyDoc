##  数据库资料

- 学生表

```sql
CREATE TABLE `stu` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `sname` char(10) DEFAULT NULL,
  `class_id` int(11) DEFAULT NULL,
  `birthday` datetime DEFAULT NULL,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `sex` enum('男','女') DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=11 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='学生表';
```

- 文章表

```sql
CREATE TABLE `article` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `title` varchar(100) NOT NULL,
  `publish_time` datetime DEFAULT NULL,
  `status` tinyint(4) DEFAULT '1',
  `click` int(11) NOT NULL DEFAULT '0',
  `flag` set('推荐','置顶','热门','图文') DEFAULT NULL,
  `stu_id` int(11) NOT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=6 DEFAULT CHARSET=utf8 ROW_FORMAT=DYNAMIC COMMENT='文章表';
```

```shell
# 学生表信息
1	李广	1	1998-02-12 08:22:13	2019-07-20 14:22:16	男
2	何青	1	1985-07-22 18:19:13	2019-07-17 21:50:38	女
3	钱佳	3	1989-11-17 10:29:13	2019-07-17 20:54:14	男
4	刘玉	1	1999-07-03 19:46:13	2019-07-17 20:54:14	女
5	后盾人	2	2003-09-01 20:33:13	2019-07-20 16:41:32	男
6	张云	3	1996-09-01 20:33:13	2019-07-19 12:59:40	女
7	李风	1	2003-02-15 20:33:13	2019-07-20 14:30:02	男
8	李兰	2		2019-07-19 12:50:07	女
9	李月	1		2019-07-18 17:49:03	女
10	刘雷		1996-11-08 20:33:13	2019-07-20 15:59:28
# 文章表信息
1	PHP很好学习，功能强大	2019-06-12 18:00:00	1	18		1
2	Mysql系统课程正在更新	2019-07-12 18:00:00	1	18		3
3	后盾人在线学习编程		1	100	推荐	5
4	文档库在doc.houdunren.com		1	100	推荐,置顶,图文	2
5	php		1	100	推荐,置顶,图文	1
```

> 一个学生能发布多篇文章但是我们只想获取关联表中的 文章表中的click点击量最多的一条，
>
> 通常情况下应为关联会返回所有的数据

## 代码逻辑

```shell
 $stu = Stu::leftJoin(\DB::raw('(select stu_id, MAX(click) AS click from article GROUP BY stu_id) AS article'), 
 \DB::raw('stu.id'), \DB::raw('article.stu_id'))
            ->get();
        return response()->json($stu);
```



- 对应原生sql语句

```shell
select * from `stu` left join (select stu_id, MAX(click) AS click from article GROUP BY stu_id) AS article on stu.id = article.stu_id
```

