# 点图验证码

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://learnku.com/articles/61532)  [link](https://www.thinkphp.cn/code/4207.html) |

**说明**

> 目前没有做过这一块,但是了解一下原理还是好的

**效果图片**

![1652167522.jpg](https://s2.loli.net/2022/05/10/kI629M7a5ZQwcLA.png)

**原理示例**

```php
# 调用返回图片
<?php
$model = new Model('vif_type'); # 实例化类别模型 
# 1、先获得随机出来的分类
$find  = $model->order('rand()')->find(); # 返回类别例如上图的螃蟹

# 2、根据随机分类，获得指定数量的图片，你想几张都可以，越多越安全
$totalNum=9;# 图片总数
$num   = 3; # 返回随机类别数量的图片(可配置化)
$model = new Model('vif_img'); # 实例化类别图片类
$yes_list  = $model->field('vi_id, vi_url')->where('vt_id','E',$find['vt_id'])->order('rand()')->limit($num)->select();
# 3、再随机，获得4张除了指定分类外的其他图片
$no_list  = $model->field('vi_id, vi_url')->where('vt_id','NEQ',$find['vt_id'])->order('rand()')->limit($totalNum-$num)->select();
$obj = new Vif();
# 4、调用验证码类库：将两组图片随机打乱合并成一组新数据
$img_list = $obj->MergeImg($yes_list, $no_list, $find['vt_title']);
return compact('find','合并的图片')
# 验证
/**
 * 将用户选择的图片三张(或者别的数量)id&返回的类别id加以查询如果正确(数量&选择的类别图片正确) session比较如果正确然后可以下一步
 */
```

