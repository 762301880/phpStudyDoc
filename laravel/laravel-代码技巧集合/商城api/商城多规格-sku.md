#  说明

> 最近看到了商城的多规格设计，这一方面网络上资料有点少所以很值得研究一下
>
> [**sku百度介绍**](https://baike.baidu.com/item/%E6%9C%80%E5%B0%8F%E5%AD%98%E8%B4%A7%E5%8D%95%E4%BD%8D/892217?fromtitle=SKU&fromid=5016808&fr=aladdin)
>
> SKU全称为Stock Keeping Unit（库存量单位），即库存进出计量的基本单元，可以是以件，盒，托盘等为单位。SKU这是对于大型连锁超市[DC](https://baike.baidu.com/item/DC/2264095)（配送中心）物流管理的一个必要的方法。现在已经被引申为产品统一编号的简称，每种产品均对应有唯一的SKU号。[单品](https://baike.baidu.com/item/单品/4841569)：对一种商品而言，当其品牌、型号、配置、等级、花色、包装容量、单位、生产日期、保质期、用途、价格、产地等属性中任一属性与其他商品存在不同时，可称为一个单品。

# 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link ](https://segmentfault.com/q/1010000005347571/a-1020000005347916) [link](https://www.kancloud.cn/xiak/php-node/248550) [link](https://cloud.tencent.com/developer/article/1464655) [link ](https://zhuanlan.zhihu.com/p/138756697) [link](https://www.zongscan.com/demo333/96065.html) [link](https://www.bbsmax.com/A/amd0baOjJg/) |
| sku介绍    | [link](https://baike.baidu.com/item/%E6%9C%80%E5%B0%8F%E5%AD%98%E8%B4%A7%E5%8D%95%E4%BD%8D/892217?fromtitle=SKU&fromid=5016808&fr=aladdin) [link](https://wenku.baidu.com/view/2fadb6062c60ddccda38376baf1ffc4fff47e273.html) |

**多规格示例图片**

> 例如我们购买手机  机身颜色 套餐类型 储存容量，不同的规格显示的价格也是不一样的

<img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/Zu5hepCGFmWktDK.png" alt="1651131356(1).jpg" style="zoom: 80%;" />

# **数据库设计**

## sku属性表

```sql
CREATE TABLE `spec_attr` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '属性名称',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='规格属性';


CREATE TABLE `spec_value` (
  `id` int NOT NULL AUTO_INCREMENT,
  `spec_attr_id` int NOT NULL COMMENT '关联规格属性id',
  `value_name` varchar(20) NOT NULL COMMENT '属性值',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='属性值表';


CREATE TABLE `spec` (
  `id` int NOT NULL AUTO_INCREMENT,
  `original_price` decimal(10,2) DEFAULT NULL COMMENT '原价',
  `price` decimal(10,2) NOT NULL COMMENT '价格(规格价格)',
  `num` int NOT NULL COMMENT '总数量(不变的上架数量)',
  `quantity` int DEFAULT NULL COMMENT '库存 (初始库存==上架数量要考虑各种情况的自增自减等)',
  `freight` decimal(10,2) DEFAULT NULL COMMENT '运费',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  `spec_attr_ids` varchar(20) DEFAULT NULL COMMENT '规格关联属性id  例如 1,2,3',
  `spec_value_ids` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '规格关联属性值ids  例如1,2,3',
  `express_fee` decimal(2,0) DEFAULT NULL COMMENT '快递费',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='规格表';
```

