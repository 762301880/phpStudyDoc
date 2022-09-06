# 叙述&资料

 ## 叙述

> 以前写过阿里云oss的功能但是用的是第三方的composer扩展包,第三方的东西
>
> 我们都懂得傻瓜化操作，今天尝试一下使用原生sdk实现阿里云上传oss

## 资料

| name                       | url                                                        |
| -------------------------- | ---------------------------------------------------------- |
| 阿里云文档中心-对象存储oss | [link](https://help.aliyun.com/document_detail/85580.html) |
|                            |                                                            |

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
        $file_name = date('YmdHis') . uniqid() .$file->getClientOriginalName();//上传的文件名称
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

# 问题示例

## oss配置生命周期

**需要删除的图片**

> 由图片可见我们需要删除的图片未知在**temp临时目录下**

![1655685898492.jpg](https://s2.loli.net/2022/06/20/1R3UpWJQor6eG28.png)

**配置生命周日**

> 点击左侧导航栏的**基础设置-生命周期** <font color="color=#dddd00">**创建规则**</font>  配置匹配的前缀(**可以通过点击图片查看链接查看前缀**),然后设置**文件过期策略删除**即可

![1655685309610.jpg](https://s2.loli.net/2022/06/20/lgOmPr7GVqCNYFs.png)







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



