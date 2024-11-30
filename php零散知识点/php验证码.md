# 点图验证码

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://learnku.com/articles/61532)  [link](https://www.thinkphp.cn/code/4207.html) |

**说明**

> 目前没有做过这一块,但是了解一下原理还是好的

**效果图片**

![1652167522.jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/kI629M7a5ZQwcLA.png)

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
# 缓存验证码key(key可以随机生成),value=验证码对应id(对应类别的id:md5加密) 过期时间15秒
$captcha_key=$this->randNum;
\Cache::set($randNum,md5('正确的id'),15)
# 返回结果 类别,验证码key(用于前端直接传递key查询缓存获取结果,这里相当于不用session去判断用户)    
return compact('find','captcha_key','合并的全部图片')
# 验证
/**
 * 将用户选择的图片三张(或者别的数量)id&返回的类别id加以查询如果正确(数量&选择的类别图片正确) session或者缓存比较如果正确然后可以下一步
 */
```

**随机生成不重复的key**

> 为了更快速查询可以加上**类别**当作缓存前缀

```php
# 示例-uuid当作key  
public function randNum($type=null)
    {
        mt_srand((double)microtime() * 10000);
        $charid = strtolower(md5(uniqid(rand(), true)));
        $hyphen = '-';
        $uuidV4 =
            substr($charid, 0, 8) . $hyphen .
            substr($charid, 8, 4) . $hyphen .
            substr($charid, 12, 4) . $hyphen .
            substr($charid, 16, 4) . $hyphen .
            substr($charid, 20, 12);
        return $uuidV4;
    }
# 示例-2
public function randNum($type=null){
  /**
   * 唯一id+随机数
   */
  return uniqid('ver_id',false).rand(0000,9999);
}
```

# 通过抓包分析业务实现

##  **请求返回对应的图片**

```shell
# 请求接口
https://shop.bangsales.com/api/user/account/sendSms?mobile=17538397579&sms_type=login&src=ZXZ
```

**返回数据**

> 可以看出 **title**对应需要选择的 标题图片
>
> **anser**  是所有的答案选项

```shell
{
  "code": 200,
  "msg": "success",
  "message": "success",
  "data": {
    "title": "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/1170151715674a74728a61d6.34669241.png",
    "anser": [
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/956270388674a74728a9203.11358865.png",
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/1337095556674a74728ac3f3.79773408.png",
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/990985005674a74728acc30.43073560.png",
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/743413645674a74728ac772.64311903.png",
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/94542530674a74728abfd1.29660816.png",
      "https://shop.bangsales.com/api/user/images/picVerify/953c9fd2136f1abb4fc9280bb359eadf/1986769679674a74728a81c6.41232000.png"
    ]
  }
}
```

## 提交请求

```shell
https://shop.bangsales.com/api/user/account/sendSms?mobile=17538397579&sms_type=login&pic=https%3A%2F%2Fshop.bangsales.com%2Fapi%2Fuser%2Fimages%2FpicVerify%2F953c9fd2136f1abb4fc9280bb359eadf%2F1642003216674a748fde4645.14611068.png&src=ZXZ
```

**失败请求返回**

```json
{
  "code": 200,
  "msg": "success",
  "message": "success",
  "data": {
    "un_verify": 1,
    "msg": "验证码失效，请重新获取"
  }
}
```

**成功返回**

```json
{
    "code": 200,
    "msg": "success",
    "message": "success",
    "data": [

    ]
}
```

## 简单刨析业务代码实现

> 每次随机的返回一个答案标题 
>
> 然后封装出几个随机图片 随机不重复的图片 但是其中有一个图片对应 答案标题  (这里进行缓存 可以用redis 也可以用 系统缓存 将携带过来的手机号跟答案图片进行绑定  可以缓存添加有效时间 进行失效校验)
>
> 再次请求提交 进行校验   因为参数里携带了  手机号 这个时候就可以 进行图片校验  如果比对成功  通过 反之 失败