# 统一上传文件到服务器

## larave

```php
        $file = $request->file('file'); //获取上传的文件
        $file_suffix = $file->getClientOriginalExtension(); //获取图片后缀(jpg,png)
        if (!in_array($file_suffix, ['jpg', 'jpeg', 'png'])) throw new \Exception("图片后缀必须是图片格式");
        $move_path = public_path("/temp_imgs"); //需要上传的临时目录位置
        if (!is_dir($move_path)) mkdir($move_path);//如果目录不存在默认创建一个
        $move_file_name = date('YmdHis') . '.' . $file_suffix;
        $file->move($move_path, $move_file_name); //转移文件
        $path_file_name = $move_path . '/' . $move_file_name;//全路径+上传的文件名
        if (!file_exists($path_file_name)) throw new \Exception("文件上传失败");

```



# 叙述&资料

 ## 叙述

> 以前写过阿里云oss的功能但是用的是第三方的composer扩展包,第三方的东西
>
> 我们都懂得傻瓜化操作，今天尝试一下使用原生sdk实现阿里云上传oss

## 资料

| name                               | url                                                          |
| ---------------------------------- | ------------------------------------------------------------ |
| 阿里云文档中心-对象存储oss         | [link](https://help.aliyun.com/document_detail/85580.html)   |
| 查看 公共云下OSS各地域Endpoint如下 | [link](https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.605c273bVxKtaM#concept-zt4-cvy-5db) |

# 逻辑示例

## 安装阿里云oss

- 使用composer[安装](https://help.aliyun.com/document_detail/85580.html?spm=a2c4g.11186623.6.1006.6ea926fdpa6BHm)

```php
composer require aliyuncs/oss-sdk-php
```

## 代码示例

###  上传示例

> 这里只是演示，正式环境我们需要两个目录 
>
> 目录一 临时目录(temp):用户上传图片但是没有提交保存到数据库的目录
>
> 目录二 上传目录(upload):用户提交上传表单携带过来的临时图片地址这里我们可以查询一遍临时目录然后再上传正式目录
>
> **这里推荐使用文档-管理文件-判断文件是否存在+拷贝文件实现**
>
> **补充**
>
> 可以去oss后台**基础设置-生命周期中设置临时目录图片过期时间然后自动删除**

```php
<?php
namespace App\Http\Controllers;

use App\Traits\ApplyResponseLayout;
use Illuminate\Http\Request;
use OSS\Core\OssException;
use OSS\OssClient;

class OssController extends Controller
{
    use ApplyResponseLayout;

    protected $accessKeyId = "<yourAccessKeyId>";
    protected $accessKeySecret = "<yourAccessKeySecret>";
    protected $endpoint = "http://oss-cn-hangzhou.aliyuncs.com"; # 对照表 https://help.aliyun.com/document_detail/31837.html

    public function upload(Request $request)
    {
        $file = $request->file('img');
        $file_name = $file->getClientOriginalName();//上传的文件名称
        $bucket = 'yaoliuyang-test-oss'; //bucket名称
        $object = $file_name;
        $content = $file->getContent();
        try {
            $ossClient = new OssClient($this->accessKeyId, $this->accessKeySecret, $this->endpoint);
            $res = $ossClient->putObject($bucket, $object, $content);
            dd($res);
        } catch (OssException $exception) {
            return $this->error($exception->getMessage());
        }
    }
}

# uploadFile() 方法上传
public function upload(Request $request)
    {
        $file = $request->file('img');
        $path      = $file->getPath() . '/' . $file->getFilename();//得到文件主机上的地址
        $file_name = date('YmdHis') . '_' . uniqid() . '_' . $file->getClientOriginalName();//上传的文件名称
        $bucket = 'yaoliuyang-test-oss'; //bucket名称
        $object = $file_name;
        # $object = $this->temp_path . $file_name;    temp_path=='temp_img/' 可配置化
        try {
            $ossClient = new OssClient($this->accessKeyId, $this->accessKeySecret, $this->endpoint);
            $res = $ossClient->uploadFile($bucket, $object, $path);
            dd($res);
        } catch (OssException $exception) {
            return $this->error($exception->getMessage());
        }
    }
```



### 储存空间

[**创建存储空间:bucket**](https://help.aliyun.com/document_detail/32102.html)

```php
 $file = $request->file('img');
        $path      = $file->getPath() . '/' . $file->getFilename();//得到文件主机上的地址
        $file_name = $file->getClientOriginalName();//上传的文件名称
        $bucket = 'examplebucket'; //bucket名称不能与网络重复
        $object = $file_name;
        try {
            $ossClient = new OssClient($this->accessKeyId, $this->accessKeySecret, $this->endpoint);
            $options = array(
               # OssClient::OSS_STORAGE => OssClient::OSS_STORAGE_IA # 这里是设置低频访问类型(文件保存30天),默认是标准类型
            );
            // 设置Bucket的读写权限为公共读，默认是私有读写。
            $res=$ossClient->createBucket($bucket, OssClient::OSS_ACL_TYPE_PUBLIC_READ, $options);
            dd($res);
        } catch (OssException $exception) {
            return $this->error($exception->getMessage());
        }
```

[**列出所有的储存空间:bucket**](https://help.aliyun.com/document_detail/146332.html)

```php
$ossClient = new OssClient($this->accessKeyId, $this->accessKeySecret, $this->endpoint);
$res = $ossClient->listBuckets()->getBucketList();
```

###  给图片添加文字水印

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 文档地址 | [link](https://help.aliyun.com/document_detail/44957.html)  [link](https://help.aliyun.com/document_detail/44957.html#watermark) |
| 他人博客 | [link](https://jinzhijun.cn/develop/346)                     |

**代码示例**

```php
// 阿里云账号AccessKey拥有所有API的访问权限，风险很高。强烈建议您创建并使用RAM用户进行API访问或日常运维，请登录RAM控制台创建RAM用户。
        $accessKeyId = "";
        $accessKeySecret = "";
// yourEndpoint填写Bucket所在地域对应的Endpoint。以华东1（杭州）为例，Endpoint填写为https://oss-cn-hangzhou.aliyuncs.com。
        $endpoint = "oss-cn-beijing.aliyuncs.com";
// 填写Bucket名称，例如examplebucket。
        $bucket = "yaoliuyang-test-oss";
// 填写Object完整路径，例如exampledir/exampleobject.jpg。Object完整路径中不能包含Bucket名称。
        $object = "312be0e5cae93d373d362d589f434215%20.png";
        $ossClient = new OssClient($accessKeyId, $accessKeySecret, $endpoint);
// 生成一个带水印参数的签名的URL，有效期是3600秒，可以直接使用浏览器访问。
        $timeout = 3600;
        function base64url_encode($data)
        {
            return rtrim(strtr(base64_encode($data), '+/', '-_'), '=');
        }

// 填写文字水印内容（例如Hello World）或者水印图片完整路径（例如panda.jpg）。
// 在图片中添加水印图片时，请确保水印图片已保存在图片所在Bucket中。
        $image_date = date('Y-m-d H:i:s');
        $image_address = "广东省东菀市xx区xx详细地址";
        $content = "$image_date";
        $content2 = "$image_address";
        $string = base64url_encode($content);
        $string2 = base64url_encode($content2);
        //dd($string);
        $image_object = "f641c9392adccecf520d66bd0150ae96.jpeg";
        // 为图片添加水印。

        $put_content = "image/watermark,text_" . $string . ',' . 'color_FFFFFF' . ',' . 't_50,x_10,y_10/watermark';
        $put_content = $put_content . ",text_" . $string2 . ',' . 'color_FFFFFF' . ',' . 't_50,x_100,y_100';
        if (!empty($image_object)) $put_content = $put_content . ',image_' . base64url_encode($image_object);
        if (!empty($image_object)) $put_content = $put_content . ',order_1';
        $options = array(
            OssClient::OSS_PROCESS => $put_content,
        );
        $signedUrl = $ossClient->signUrl($bucket, $object, $timeout, "GET", $options);
        dd($signedUrl);
```



#### 问题记录

**如何给文字水印换行**

```shell
https://blog.csdn.net/qq_36025814/article/details/124158528
```

####  添加文字水印时提示“font content is too large”怎么办？

> https://help.aliyun.com/document_detail/44957.htm#p-ma2-j0n-4mi
>
> 通过OSS的图片处理为图片添加文字水印时，最长不能超过64个字符（1个汉字计为3个字符）。当提示“font content is too large”时，建议您缩短文字长度，然后为图片添加文字水印。更多信息，请参见[示例一：添加文字水印](https://help.aliyun.com/document_detail/44957.htm#section-tj2-dbv-vdb)。

### 图片处理

#### [质量转换](https://help.aliyun.com/zh/oss/user-guide/adjust-image-quality?spm=a2c4g.11186623.0.0.8c9a2b11Rz30BN)

> **阿里云oss读取的时候如何压缩图片**

> 阿里云对象存储（OSS）本身并不提供直接在存储中压缩图片的功能。然而，你可以在读取图片时使用一些第三方工具或库来进行图片压缩。以下是一种常见的方法，使用阿里云的图片处理服务（OSS Image Service）来在读取时压缩图片：
>
> 1. **启用OSS图片服务：** 在阿里云控制台中，找到你的OSS Bucket，进入"管理" -> "图片处理"，然后启用图片处理服务。
>
> 2. **使用图片处理参数：** 在读取图片的URL中，添加相应的图片处理参数进行压缩。例如，可以使用以下参数：
>
>    - `x-oss-process=image/resize,w_500`：将图片宽度调整为500像素，高度按比例缩放。
>    - `x-oss-process=image/format,jpg`：将图片格式转换为JPG。
>
>    组合这些参数，你可以创建一个包含所有所需处理的URL，示例如下：

```shell
https://your-bucket-name.oss-cn-hangzhou.aliyuncs.com/your-image.jpg?x-oss-process=image/resize,w_500/format,jpg
```

> 请替换 "your-bucket-name" 和 "your-image.jpg" 为实际的Bucket名称和图片路径。



# 问题示例

## oss配置生命周期

**需要删除的图片**

> 由图片可见我们需要删除的图片未知在**temp临时目录下**

![1655685898492.jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1R3UpWJQor6eG28.png)

**配置生命周日**

> 点击左侧导航栏的**基础设置-生命周期** <font color="color=#dddd00">**创建规则**</font>  配置匹配的前缀(**可以通过点击图片查看链接查看前缀**),然后设置**文件过期策略删除**即可

![1655685309610.jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/lgOmPr7GVqCNYFs.png)







##  oss配置生命周期未生效原因

[**参考资料**](https://help.aliyun.com/document_detail/326351.html)

>## 详细信息
>
>生命周期规则创建后的24小时内，OSS会加载规则。规则加载完成后，OSS会在每天的北京时间`8:00`开始执行规则，并在随后的24小时内执行完毕。Object的最后修改时间与生命周期规则开始执行时间（8:00）必须间隔24小时以上，请在耐心等待。例如生命周期规则为Object上传1天后删除，则2020年7月20日上传的文件删除时间如下：
>
>- 北京时间8:00前上传的文件会在2020年7月21日8:00开始删除，并在7月22日8:00前删除完毕。
>- 北京时间8:00后上传的文件会在2020年7月22日8:00开始删除，并在7月23日8:00前删除完毕。
>
>如果在48小时后，生命周期规则还是未生效，请参见[生命周期配置示例](https://help.aliyun.com/document_detail/160576.htm)，检查生命周期规则配置是否正确。
>
>> **注意**：更新生命周期规则会中止当天的生命周期任务，请不要频繁更新生命周期规则。##



# [跨域资源共享](https://help.aliyun.com/document_detail/32110.html)

```php
   $corsConfig = new CorsConfig();
   $rule = new CorsRule();


 // 设置允许跨域请求的响应头。AllowedHeader可以设置多个，每个AllowedHeader中最多只能使用一个通配符星号（*）。
// 建议无特殊需求时设置AllowedHeader为星号（*）。
        $rule->addAllowedHeader("*");
// 设置允许用户从应用程序中访问的响应头。ExposeHeader可以设置多个，ExposeHeader中不支持使用通配符星号（*）。
        $rule->addExposeHeader("x-oss-header");

// 设置AllowedOrigin为星号（*）时，表示允许所有域的来源。
        $rule->addAllowedOrigin("*");
// 设置允许的跨域请求方法。
        $rule->addAllowedMethod("GET");
        $rule->addAllowedMethod("POST");
        $rule->addAllowedMethod("PUT");
        $rule->addAllowedMethod("DELETE");
        $rule->addAllowedMethod("HEAD");
// 设置浏览器对特定资源的预取（OPTIONS）请求返回结果的缓存时间，单位为秒。
        $rule->setMaxAgeSeconds(0);
        
// 每个Bucket最多支持添加10条规则。
        $corsConfig->addRule($rule);
        $ossClient = new OssClient($this->accessKeyId, $this->accessKeySecret, $this->endpoint);
        // 已存在的规则将被覆盖。
        $res = $ossClient->putBucketCors($bucket, $corsConfig);
        dd($res);
```

# oss客户端

## oss browser

> [下载](https://oss.console.aliyun.com/services/tools)

![image-20240412141557217](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412141557217.png)

![image-20240412142052985](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412142052985.png)

# 重要事情记录

<font color='red'>记住千万不要用超级用户的accessKey会产生盗号非法创建资源风险  20240411被人盗用超级账户的accesskey 之后开通了几百个ECL实例产生了大量费用
</font>