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
 # FROM_UNIXTIME('z','%Y-%m-%d')
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
    public static function getFieldNames($exceptField = [])
    {
        $selfModel = self::field($exceptField, true)->find();
        $selfModel = !empty($selfModel) ? $selfModel->toArray() : [];
        return array_keys($selfModel);
    }
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

![image-20220728113847340](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20220728113847340.png)

**代码示例**

```shell
UPDATE "表名" SET "需要修改的字段名称" = REPLACE ( "需要修改的字段名称", "需要修改的文字(例如:咨询)", "修改后的文字(例如:询问)" ) WHERE cate = "表名"
```

## 插入一百万条数据

**参考资料**

| 名称               | 地址                                                         |
| ------------------ | ------------------------------------------------------------ |
| 参考文档           | [link](https://mp.weixin.qq.com/s/MArWD5iDi6Sv0r0vfebt5A)  [文档备份PDF](https://gitee.com/yaolliuyang/pdf_doc_backup/raw/master/%E4%B8%BA%E4%BA%86%E5%87%8F%E5%B0%91%E5%BB%B6%E8%BF%9F%E5%92%8C%E5%8D%A1%E9%A1%BF%EF%BC%8C%E6%88%91%E5%AF%B9%20MySQL%20%E6%9F%A5%E8%AF%A2%E5%81%9A%E4%BA%86%E8%BF%99%E4%BA%9B%E4%BC%98%E5%8C%96%E5%A4%84%E7%90%86..._.pdf) |
| 生成随机数文档参考 | [link](https://zhuanlan.zhihu.com/p/386664858)  [link](https://blog.csdn.net/xiaojin21cen/article/details/103661435) |



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
SELECT mock_data();
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

