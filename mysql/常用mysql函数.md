## [**返回当年年份**](https://blog.csdn.net/chengmouyu7082/article/details/100910188)

```sql
SELECT DATE_FORMAT(NOW(), '%Y') FROM DUAL ;
```

## **时间戳转化为时间**

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_34062469/article/details/86361724) |

**代码示例**

```sql
 -- FROM_UNIXTIME('z','%Y-%m-%d')
 FROM_UNIXTIME(pxs_aunt_basics.birth,'%Y-%m-%d') as birth,
```

## [**查询表中的字段**](https://blog.csdn.net/Knight_Key/article/details/122565171)

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://www.yisu.com/zixun/693352.html) [link](https://blog.csdn.net/qq_28466271/article/details/102503137) [link](https://9iphp.com/web/php/laravel-get-column-data-type.html) |

**说明**

> 查询的是数据库**information_schema**数据库中的**COLUMNS**表,这个在框架中基本无法使用
>
> 因为框架配置具体到了数据库

```php
select COLUMN_NAME from information_schema.COLUMNS where table_name = '具体表名'

    # thinkphp  TABLE_SCHEMA="数据库名称",TABLE_NAME="数据库下的表名称"
    $res=Db::query("SELECT COLUMN_NAME FROM information_schema.columns WHERE TABLE_SCHEMA='laravel_study' AND TABLE_NAME='stu'");
    $columbs=array_column($res,"COLUMN_NAME");
    dd($columbs); # 返回对应字段名称
```

**自定义orm封装**

> **使用**:可以放在**orm模型**中或者写一个**trait**模型继承使用

```php
# thinkphp 
    /**
     * 返回所有的字段名称
     * 缺点必须有一条记录才可以 需要排除的字段
     * @param array $exceptField
     * @return array
     */
    public static function getAllFieldNames($exceptField = [])
    {
        $selfModel = self::field($exceptField, true)->find();
        $selfModel = !empty($selfModel) ? $selfModel->toArray() : [];
        return array_keys($selfModel);
    }


# 方法二

## 在MySQL中，可以使用information_schema数据库中的COLUMNS表来动态获取某张表的所有字段。以下是一个示例查询：

SELECT COLUMN_NAME
FROM information_schema.COLUMNS
WHERE TABLE_SCHEMA = '数据库名' AND TABLE_NAME = '表名';
```

## 替换字段的部分内容

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/qq_42640067/article/details/118070848) |

**说明**

> **核心思想**
>
> 利用mysql的**REPLACE**函数做替换字符
>
> ```mysql
> REPLACE(s,s1,s2)	将字符串 s2 替代字符串 s 中的字符串 s1	
> 将字符串 abc 中的字符 a 替换为字符 x：    SELECT REPLACE('abc','a','x') --xbc
> ```

![image-20220728113847340](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220728113847340.png)

**代码示例**

```shell
UPDATE "表名" SET "需要修改的字段名称" = REPLACE ( "需要修改的字段名称", "需要修改的文字(例如:咨询)", "修改后的文字(例如:询问)" ) WHERE cate = "表名"
```

## 插入一百万条数据

### 说明

> **推荐采用代码批量写入 例如 10W条数据 分100 次 每次写入1000  亲测很快 十几秒就可以全部模拟写入**
>
> **例如python脚本**

**参考资料**

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 参考文档           | [link](https://mp.weixin.qq.com/s/MArWD5iDi6Sv0r0vfebt5A)  [文档备份PDF](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/%E4%B8%BA%E4%BA%86%E5%87%8F%E5%B0%91%E5%BB%B6%E8%BF%9F%E5%92%8C%E5%8D%A1%E9%A1%BF%EF%BC%8C%E6%88%91%E5%AF%B9%20MySQL%20%E6%9F%A5%E8%AF%A2%E5%81%9A%E4%BA%86%E8%BF%99%E4%BA%9B%E4%BC%98%E5%8C%96%E5%A4%84%E7%90%86..._.pdf) |
| 生成随机数文档参考 | [link](https://zhuanlan.zhihu.com/p/386664858)  [link](https://blog.csdn.net/xiaojin21cen/article/details/103661435) |

**mysql存储过程**

```sql
set global log_bin_trust_function_creators=TRUE;
DELIMITER $$
CREATE FUNCTION mock_data() 
RETURNS INT
BEGIN
     DECLARE num INT DEFAULT 1000000;
		 DECLARE i INT DEFAULT 0;
		 
		 WHILE i<num DO
		   -- 插入语句

INSERT INTO `app_user` (`name`,`email`,`phone`,`gender`,`password`,`age`) VALUES(CONCAT('用户',i),'762301880@qq.com',
CONCAT('18',FLOOR(RAND()*((999999999-100000000)+100000000))),FLOOR(RAND()*2),UUID(),FLOOR(RAND()*100));
       
			 set i= i+1;
     END WHILE;
        RETURN i;
END;

------------------------------------------------------------------------------------------------------------------------------
-- 执行函数
SELECT mock_data();


-- 删除函数
DROP FUNCTION IF EXISTS mock_data;
```

**插入示例二**

```sql
-- 表结构


CREATE TABLE `stu` (
  `id` int NOT NULL AUTO_INCREMENT,
  `sname` char(10) CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci DEFAULT NULL,
  `class_id` int DEFAULT NULL,
  `birthday` datetime DEFAULT NULL,
  `updated_at` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  `sex` enum('男','女') CHARACTER SET utf8mb3 COLLATE utf8mb3_general_ci DEFAULT NULL,
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=100011 DEFAULT CHARSET=utf8mb3 ROW_FORMAT=DYNAMIC COMMENT='学生表';


-- 执行写入过程
-- DROP PROCEDURE if exists addMyData -- 先删除再创建
CREATE PROCEDURE addMyData () BEGIN
	DECLARE
		num INT;
	
	SET num = 1;
	WHILE
			num <= 100000 DO
			INSERT INTO stu (`sname`,`class_id`,`birthday`,`updated_at`,`sex`)
		VALUES
			(
				CONCAT((CONCAT(
					SUBSTRING( '赵钱孙李周吴郑王冯陈诸卫蒋沈韩杨朱秦尤许何吕施张孔曹严华金魏陶姜戚谢邹喻柏水窦章云苏潘葛奚范彭郎鲁韦昌马苗凤花方俞任袁柳酆鲍史唐费廉岑薛雷贺倪汤滕殷罗毕郝邬安常乐于时傅皮齐康伍余元卜顾孟平黄和穆萧尹姚邵堪汪祁毛禹狄米贝明臧计伏成戴谈宋茅庞熊纪舒屈项祝董粱杜阮蓝闵席季麻强贾路娄危江童颜郭梅盛林刁钟徐邱骆高夏蔡田樊胡凌霍虞万支柯咎管卢莫经房裘干解应宗丁宣贲邓郁单杭洪包诸左石崔吉钮龚', FLOOR( 1+190 * RAND()), 1 ),
					SUBSTRING( '明国华建文平志伟东海强晓生光林小民永杰军金健一忠洪江福祥中正振勇耀春大宁亮宇兴宝少剑云学仁涛瑞飞鹏安亚泽世汉达卫利胜敏群波成荣新峰刚家龙德庆斌辉良玉俊立浩天宏子松克清长嘉红山贤阳乐锋智青跃元武广思雄锦威启昌铭维义宗英凯鸿森超坚旭政传康继翔栋仲权奇礼楠炜友年震鑫雷兵万星骏伦绍麟雨行才希彦兆贵源有景升惠臣慧开章润高佳虎根远力进泉茂毅富博霖顺信凡豪树和恩向道川彬柏磊敬书鸣芳培全炳基冠晖京欣廷哲保秋君劲轩帆若连勋祖锡吉崇钧田石奕发洲彪钢运伯满庭申湘皓承梓雪孟其潮冰怀鲁裕翰征谦航士尧标洁城寿枫革纯风化逸腾岳银鹤琳显焕来心凤睿勤延凌昊西羽百捷定琦圣佩麒虹如靖日咏会久昕黎桂玮燕可越彤雁孝宪萌颖艺夏桐月瑜沛诚夫声冬奎扬双坤镇楚水铁喜之迪泰方同滨邦先聪朝善非恒晋汝丹为晨乃秀岩辰洋然厚灿卓杨钰兰怡灵淇美琪亦晶舒菁真涵爽雅爱依静棋宜男蔚芝菲露娜珊雯淑曼萍珠诗璇琴素梅玲蕾艳紫珍丽仪梦倩伊茜妍碧芬儿岚婷菊妮媛莲娟一', FLOOR( 1+400 * RAND()), 1 ),
				SUBSTRING( '明国华建文平志伟东海强晓生光林小民永杰军金健一忠洪江福祥中正振勇耀春大宁亮宇兴宝少剑云学仁涛瑞飞鹏安亚泽世汉达卫利胜敏群波成荣新峰刚家龙德庆斌辉良玉俊立浩天宏子松克清长嘉红山贤阳乐锋智青跃元武广思雄锦威启昌铭维义宗英凯鸿森超坚旭政传康继翔栋仲权奇礼楠炜友年震鑫雷兵万星骏伦绍麟雨行才希彦兆贵源有景升惠臣慧开章润高佳虎根远力进泉茂毅富博霖顺信凡豪树和恩向道川彬柏磊敬书鸣芳培全炳基冠晖京欣廷哲保秋君劲轩帆若连勋祖锡吉崇钧田石奕发洲彪钢运伯满庭申湘皓承梓雪孟其潮冰怀鲁裕翰征谦航士尧标洁城寿枫革纯风化逸腾岳银鹤琳显焕来心凤睿勤延凌昊西羽百捷定琦圣佩麒虹如靖日咏会久昕黎桂玮燕可越彤雁孝宪萌颖艺夏桐月瑜沛诚夫声冬奎扬双坤镇楚水铁喜之迪泰方同滨邦先聪朝善非恒晋汝丹为晨乃秀岩辰洋然厚灿卓杨钰兰怡灵淇美琪亦晶舒菁真涵爽雅爱依静棋宜男蔚芝菲露娜珊雯淑曼萍珠诗璇琴素梅玲蕾艳紫珍丽仪梦倩伊茜妍碧芬儿岚婷菊妮媛莲娟一', FLOOR( 1+400 * RAND()), 1 )) ),""),
				RAND()* 3,
				DATE_ADD( CONCAT( '2023-0', CEIL( RAND()* 9 ), '-01 00:00:00' ), INTERVAL FLOOR( 1 + ( RAND() * 108000 )) SECOND ),
				DATE_ADD( CONCAT( '2023-0', CEIL( RAND()* 9 ), '-01 00:00:00' ), INTERVAL FLOOR( 1 + ( RAND() * 108000 )) SECOND ),
			IF
				( floor( rand()* 2+1 )= 1, "男", "女" ) 
			);
		
		SET num = num + 1;
		
	END WHILE;
END;

-- 调用写入

call addMyData();
```

**示例补充- 自动递增日期**

```mysql
# 添加数据库
CREATE TABLE `test` (
  `id` int NOT NULL AUTO_INCREMENT COMMENT ' ',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=14652 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci;


-- 执行写入过程
DROP PROCEDURE if exists addMyData -- 先删除再创建

-- CREATE PROCEDURE addMyData () BEGIN
-- 	DECLARE num INT;
-- 	DECLARE curr_date DATE;
-- 	
-- 	SET num = 1;
-- 	
-- 	SET curr_date = DATE( '2021-07-20' );-- 指定初始日期
-- 	WHILE
-- 			num <= 100000 DO
-- 			INSERT INTO test ( `create_time` )
-- 		VALUES
-- 			( curr_date );
-- 		
-- 		SET num = num + 1;
-- 		
-- 		SET curr_date = DATE_ADD( curr_date, INTERVAL 1 DAY );-- 增加一天
-- 		
-- 	END WHILE;
-- END;

-- call addMyData();
```

## 删除三个月前的数据

```mysql
# FROM_UNIXTIME(create_time,'%Y-%m-%d')
DELETE FROM `test` where  DATE_FORMAT(create_time,'%Y-%m-%d') <  DATE_FORMAT( DATE_SUB( CURDATE(), INTERVAL 3 MONTH ), '%Y-%m-%d' )

-- SELECT
-- 	*,
-- 	DATE_FORMAT( create_time, '%Y-%m-%d' ) AS del_time,
-- 	DATE_FORMAT( DATE_SUB( CURDATE(), INTERVAL 3 MONTH ), '%Y-%m-%d' ) AS three_months_ago
-- 
-- FROM
-- 	test
```

## EXPLAIN查询sql语句瓶颈

> EXPLAIN命令是SQL语句中的一个关键字，用于分析查询语句或表结构的性能瓶颈。它可以模拟优化器执行SQL查询语句，从而知道数据库是如何处理SQL语句的。EXPLAIN命令可以帮助我们找出查询语句的瓶颈，从而进行优化。
>
> EXPLAIN命令的基本语法如下

```sql
EXPLAIN SELECT * FROM table_name WHERE condition;
```

> 其中，`SELECT`语句是要进行分析的查询语句，`table_name`是要查询的表名，`condition`是查询条件。
>
> EXPLAIN命令的输出结果中包含了很多信息，其中比较重要的有：
>
> 1. **启动方式**：使用EXPLAIN关键字放在SQL语句之前，MySQL将不会执行该语句，而是展示查询的执行计划。
> 2. **读取顺序**：EXPLAIN可以显示MySQL读取表的顺序，这有助于我们理解查询的工作流程。
> 3. **索引使用情况**：通过EXPLAIN，我们可以看到哪些索引可以被使用，以及实际上是否被查询所使用。
> 4. **数据访问类型**：EXPLAIN会展示数据读取操作的类型，例如全表扫描、索引扫描等。
> 5. **连接类型**：如果查询涉及多个表的连接，EXPLAIN还可以展示连接类型，如内连接、外连接等。
> 6. **详细字段解释**：
>
> - id：每个查询语句都有一个唯一的id。
> - select_type：查询类型，包括SIMPLE（简单查询）、PRIMARY（主查询）、SUBQUERY（子查询）等。
> - table：要查询的表名。
> - partitions：表所属的分区。
> - type：访问类型，包括ALL（全表扫描）、index（索引扫描）、range（范围扫描）等。
> - possible_keys：可能使用的索引。
> - key：实际使用的索引。
> - key_length：使用的索引长度。
> - ref：连接条件中使用的列或常量。（显示索引的哪一列被使用了）
> - rows：扫描的行数。
> - filtered：按表条件过滤的行数。
> - Extra：包含不适合在其他列中显示的额外信息，如Using index（使用覆盖索引）、Using filesort（使用文件排序）等。
>
> 其中，type和key是最重要的两个字段。type字段表示MySQL如何查找数据，而key字段表示MySQL实际上使用了哪个索引来查找数据。如果type为ALL，则表示MySQL没有使用任何索引来查找数据；如果type为index，则表示MySQL使用了覆盖索引（Covering Index）来查找数据；如果type为range，则表示MySQL使用了范围扫描（Range Scan）来查找数据；如果type为ref，则表示MySQL使用了索引合并扫描（Index Merge Scan）来查找数据。而key字段则表示MySQL实际上使用了哪个索引来查找数据。如果key为NULL，则表示MySQL没有使用任何索引来查找数据；如果key为const，则表示MySQL使用了主键或唯一索引来查找数据；如果key为system，则表示MySQL只扫描了1行数据，并且这1行数据就是system表中的唯一数据。

###  type类型详解

> type 列表示连接类型，它指明了 MySQL 如何找到行

#### **eq_ref**

- 这是最佳的连接类型之一，通常出现在使用主键或唯一索引进行连接时。
- 对于前一个表中的每一个记录组合，从这个表中读取一行。
- 简单来说，就是对于每个匹配行，MySQL 都能在索引中找到一个唯一的行。这通常发生在使用 = 操作符并且右边是一个常量或者前一个表中的列时。

- 例如，在两个表通过主键或唯一键连接的情况下，eq_ref 表示 MySQL 可以直接根据索引找到唯一的一行数据，这是非常高效的连接方式。

#### **ref **

> ref 在 EXPLAIN 输出中的全称是 referenced column or constant，即“引用的列或常量”

在 EXPLAIN 输出中，ref 列表示 MySQL 在查询过程中用于与索引进行比较的列或常量。具体来说：

ref

- 这一列表示了 MySQL 使用哪个列或常量值来查找索引中的记录。
- 如果 type 是 eq_ref、ref、ref_or_null 等类型，ref 列会显示实际用来比较的列名或常量。

示例解释
假设有一个查询如下：

```mysql
SELECT * FROM orders WHERE customer_id = 123;
```

在这个查询中，如果 customer_id 上有索引，并且 EXPLAIN 的输出显示 type 为 ref，那么 ref 列可能会显示 const，表示使用的是一个常量值（即 123）来进行索引查找。
再比如：

```mysql
SELECT * FROM orders o JOIN customers c ON o.customer_id = c.id;
```

如果 EXPLAIN 显示 type 为 eq_ref 或 ref，那么 ref 列可能会显示 c.id，表示使用 customers 表中的 id 列来进行索引查找。

总结

- ref 列的内容取决于具体的查询和连接条件。
- 它告诉你是哪些列或常量被用来查找索引中的记录。
- 对于 eq_ref 类型，ref 列通常显示前一个表中的列或常量，确保每个匹配行都能通过索引找到唯一的一行。