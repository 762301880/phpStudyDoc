#  说明

> 最近看到了商城的多规格设计，这一方面网络上资料有点少所以很值得研究一下
>
> [**sku百度介绍**](https://baike.baidu.com/item/%E6%9C%80%E5%B0%8F%E5%AD%98%E8%B4%A7%E5%8D%95%E4%BD%8D/892217?fromtitle=SKU&fromid=5016808&fr=aladdin)
>
> SKU全称为Stock Keeping Unit（库存量单位），即库存进出计量的基本单元，可以是以件，盒，托盘等为单位。SKU这是对于大型连锁超市[DC](https://baike.baidu.com/item/DC/2264095)（配送中心）物流管理的一个必要的方法。现在已经被引申为产品统一编号的简称，每种产品均对应有唯一的SKU号。[单品](https://baike.baidu.com/item/单品/4841569)：对一种商品而言，当其品牌、型号、配置、等级、花色、包装容量、单位、生产日期、保质期、用途、价格、产地等属性中任一属性与其他商品存在不同时，可称为一个单品。
>
> <font color='red'>核心思想:笛卡尔积算法</font>

# 资料

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link ](https://segmentfault.com/q/1010000005347571/a-1020000005347916) [link](https://www.kancloud.cn/xiak/php-node/248550) [link](https://cloud.tencent.com/developer/article/1464655) [link ](https://zhuanlan.zhihu.com/p/138756697) [link](https://www.zongscan.com/demo333/96065.html) [link](https://www.bbsmax.com/A/amd0baOjJg/)  [link](https://learnku.com/articles/63323) |
| sku介绍    | [link](https://baike.baidu.com/item/%E6%9C%80%E5%B0%8F%E5%AD%98%E8%B4%A7%E5%8D%95%E4%BD%8D/892217?fromtitle=SKU&fromid=5016808&fr=aladdin) [link](https://wenku.baidu.com/view/2fadb6062c60ddccda38376baf1ffc4fff47e273.html) |

**多规格示例图片**

> 例如我们购买手机  机身颜色 套餐类型 储存容量，不同的规格显示的价格也是不一样的

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/Zu5hepCGFmWktDK.png" alt="1651131356(1).jpg" style="zoom: 80%;" />

# **数据库设计**

## sku属性表

>这种设计可以用于商品SKU，但是需要根据具体业务场景进行优化。其中，`spec_attr` 表用于存储规格属性，`spec_value` 表用于存储规格属性值，而 `spec` 表用于存储商品的规格信息。
>
>通过 `spec_attr` 和 `spec_value` 的表结构设计，可以灵活地管理商品规格属性和属性值。但是在实际业务场景中，考虑到规格数量较多时，会存在以下问题：
>
>

```sql
CREATE TABLE `spec_attr` (
  `id` int NOT NULL AUTO_INCREMENT,
  `name` varchar(20) NOT NULL COMMENT '属性名称', -- 例如 颜色  内存  型号
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='规格属性';


CREATE TABLE `spec_value` (
  `id` int NOT NULL AUTO_INCREMENT,
  `spec_attr_id` int NOT NULL COMMENT '关联规格属性id', -- 例如 黑色得属性就是(白色,黑色...),内存(64G,128G...),型号(pro,max...)
  `value_name` varchar(20) NOT NULL COMMENT '属性值',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='属性值表';


CREATE TABLE `spec` (
  `id` int NOT NULL AUTO_INCREMENT,
  `product_id` int NOT NULL COMMENT '关联商品id',
  `original_price` decimal(10,2) DEFAULT NULL COMMENT '原价',
  `price` decimal(10,2) NOT NULL COMMENT '价格(规格价格)',
  `num` int NOT NULL COMMENT '总数量(不变的上架数量)',
  `quantity` int DEFAULT NULL COMMENT '库存 (初始库存==上架数量要考虑各种情况的自增自减等)',
  `freight` decimal(10,2) DEFAULT NULL COMMENT '运费',
  `create_time` datetime DEFAULT NULL,
  `update_time` datetime DEFAULT NULL,
  `spec_attr_ids` varchar(20) DEFAULT NULL COMMENT '规格关联属性id  例如 1,2,3',
  `spec_value_ids` varchar(20) CHARACTER SET utf8mb4 COLLATE utf8mb4_0900_ai_ci DEFAULT NULL COMMENT '规格关联属性值ids  “颜色:红色|尺码:M',
  `express_fee` decimal(2,0) DEFAULT NULL COMMENT '快递费',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_0900_ai_ci COMMENT='商品sku表';
```

**对应代码逻辑示例**



```php
<?php

class skuService {
  // 数据库连接句柄
  private $conn;

  public function __construct($db) {
    $this->conn = $db;
  }

  /**
   * 获取所有商品的 SKU 信息
   */
  public function getAllSkus($product_id) {
    $sql = "SELECT * FROM sku WHERE product_id = ? ORDER BY id DESC";
    $stmt = $this->conn->prepare($sql);
    $stmt->bindParam(1, $product_id);
    $stmt->execute();
    return $stmt->fetchAll(PDO::FETCH_ASSOC);
  }

  /**
   * 根据属性值获取 SKU 信息
   */
  public function getSkuByAttrValues($product_id, $attr_values) {
    // 将属性值按照升序排序，并拼接为字符串
    sort($attr_values);
    $attr_values_str = implode('|', $attr_values);
    // 查询匹配的 SKU 信息
    $sql = "SELECT * FROM sku WHERE product_id = ? AND attr_values = ?";
    $stmt = $this->conn->prepare($sql);
    $stmt->bindParam(1, $product_id);
    $stmt->bindParam(2, $attr_values_str);
    $stmt->execute();
    return $stmt->fetch(PDO::FETCH_ASSOC);
  }

  /**
   * 添加商品 SKU 信息
   */
  public function addSku($product_id, $price, $num, $attr_values) {
    // 将属性值按照升序排序，并拼接为字符串
    sort($attr_values);
    $attr_values_str = implode('|', $attr_values);
    // 插入新的 SKU 信息
    $sql = "INSERT INTO sku (product_id, price, num, attr_values) VALUES (?, ?, ?, ?)";
    $stmt = $this->conn->prepare($sql);
    $stmt->bindParam(1, PDO::PARAM_INT);
    $stmt->bindParam(2, PDO::PARAM_STR);
    $stmt->bindParam(3, PDO::PARAM_INT);
    $stmt->bindParam(4, PDO::PARAM_STR);
    $stmt->execute([$product_id, $price, $num, $attr_values_str]);
    return $this->conn->lastInsertId();
  }

  /**
   * 更新商品 SKU 信息
   */
  public function updateSku($product_id, $sku_id, $price, $num, $attr_values) {
    // 将属性值按照升序排序，并拼接为字符串
    sort($attr_values);
    $attr_values_str = implode('|', $attr_values);
    // 更新 SKU 信息
    $sql = "UPDATE sku SET price = ?, num = ?, attr_values = ? WHERE id = ? AND product_id = ?";
    $stmt = $this->conn->prepare($sql);
    $stmt->bindParam(1, PDO::PARAM_STR);
    $stmt->bindParam(2, PDO::PARAM_INT);
    $stmt->bindParam(3, PDO::PARAM_STR);
    $stmt->bindParam(4, PDO::PARAM_INT);
    $stmt->bindParam(5, PDO::PARAM_INT);
    $stmt->execute([$price, $num, $attr_values_str, $sku_id, $product_id]);
    return $stmt->rowCount();
  }

  /**
   * 删除商品 SKU 信息
   */
  public function deleteSku($product_id, $sku_id) {
    $sql = "DELETE FROM sku WHERE id = ? AND product_id = ?";
    $stmt = $this->conn->prepare($sql);
    $stmt->bindParam(1, PDO::PARAM_INT);
    $stmt->bindParam(2, PDO::PARAM_INT);
    $stmt->execute([$sku_id, $product_id]);
    return $stmt->rowCount();
  }
}

?>
```

>代码解释：
>
>- `skuService` 类负责实现 SKU 相关的数据操作逻辑。
>- `__construct` 方法用于连接数据库。
>- `getAllSkus` 方法用于获取指定商品的所有 SKU 信息。
>- `getSkuByAttrValues` 方法用于根据属性值获取指定商品的 SKU 信息。
>- `addSku` 方法用于添加商品的 SKU 信息。
>- `updateSku` 方法用于更新商品的 SKU 信息。
>- `deleteSku` 方法用于删除商品的 SKU 信息。
>
>在 `addSku` 和 `updateSku` 方法中，为了方便操作和参数传递方便，采用了预处理语句并进行了占位符绑定，同时也增加了防止 SQL 注入攻击的安全性。
>
>下面是一些可能会用到的代码片段，如需要完整的应用示例请提供更多信息。

```php
// 示例：获取所有商品的 SKU 信息
$sku_service = new skuService($conn);
$product_id = 1; // 商品ID
$skus = $sku_service->getAllSkus($product_id);

// 示例：通过属性值获取商品的 SKU 信息
$sku_service = new skuService($conn);
$product_id = 1; // 商品ID
$attr_values = ['颜色:红色', '尺码:M']; // 属性值数组
$sku = $sku_service->getSkuByAttrValues($product_id, $attr_values);

// 示例：添加商品的 SKU 信息
$sku_service = new skuService($conn);
$product_id = 1; // 商品ID
$price = 100; // SKU 价格
$num = 10; // SKU 数量
$attr_values = ['颜色:红色', '尺码:M']; // 属性值数组
$sku_id = $sku_service->addSku($product_id, $price, $num, $attr_values);

// 示例：更新商品的 SKU 信息
$sku_service = new skuService($conn);
$product_id = 1; // 商品ID
$sku_id = 1; // SKU ID
$price = 120; // 更新后的 SKU 价格
$num = 15; // 更新后的 SKU 数量
$attr_values = ['颜色:蓝色', '尺码:L']; // 更新后的属性值数组
$result = $sku_service->updateSku($product_id, $sku_id, $price, $num, $attr_values);

// 示例：删除商品的 SKU 信息
$sku_service = new skuService($conn);
$product_id = 1; // 商品ID
$sku_id = 1; // SKU ID
$result = $sku_service->deleteSku($product_id, $sku_id);
```

#  扩展补充

##  [笛卡尔积算法](https://learnku.com/articles/63323)

**原数据**

```shell
[{
    "name": "颜色",
    "attr_values": "白色,黑色"
}, {
    "name": "内存",
    "attr_values": "64G,128G"
}, {
    "name": "型号",
    "attr_values": "X,pro"
}]
```

**目标数据**

```php
 [{
     "price": "0",
     "内存": "64G",
     "型号": "X",
     "颜色": "白色"
 }, {
     "price": "0",
     "内存": "64G",
     "型号": "pro",
     "颜色": "白色"
 }, {
     "price": "0",
     "内存": "128G",
     "型号": "X",
     "颜色": "白色"
 }, {
     "price": "0",
     "内存": "128G",
     "型号": "pro",
     "颜色": "白色"
 }, {
     "price": "0",
     "内存": "64G",
     "型号": "X",
     "颜色": "黑色"
 }, {
     "price": "0",
     "内存": "64G",
     "型号": "pro",
     "颜色": "黑色"
 }, {
     "price": "0",
     "内存": "128G",
     "型号": "X",
     "颜色": "黑色"
 }, {
     "price": "0",
     "内存": "128G",
     "型号": "pro",
     "颜色": "黑色"
 }]
```

**代码**

```php
<?php
/**
 * 设置规格
 *
 * @param array $attr_group  例:[['name'=>'颜色','attr_values'=>'白色,红色']]
 * @param integer $index 第几轮
 * @param integer $row 第几行
 * @param array $keys
 * @param array $attrs
 * @return array
 */
function setAttr(array $attr_group, int $index = 0, array $keys = [], array $attrs = []) 
{
    static $row = 0;

    if ($index == 0) {
        foreach($attr_group as $i) {
            $keys[] = 0;
        }
    }

    if (isset($attr_group[$index])) {
        $attr_values = explode(',', $attr_group[$index]['attr_values']);
        foreach($attr_values as $key => $attr_value) {
            $keys[$index] = $key;
            if ($index + 1 == count($attr_group)) {
                foreach($keys as $i => $item) {
                    $attrs[$row][$attr_group[$i]['name']] = explode(',', $attr_group[$i]['attr_values'])[$item];
                }
                $attrs[$row]['price'] = '0';
                $row++;
            } else {
                $attrs = setAttr($attr_group, $index + 1, $keys, $attrs);
            }
        }
    }

    return $attrs;
}

//调用
$attr_group = [
    [
        'name' => '颜色',
        'attr_values' => '白色,红色'
    ], 
    [
        'name' => '内存',
        'attr_values' => '64G,128'
    ]
];
$attrs = setAttr($attr_group);
var_dump($attrs);

?>
```

# 实战参考

![image-20231226172131058](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231226172131058.png)



![image-20231226172157060](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231226172157060.png)