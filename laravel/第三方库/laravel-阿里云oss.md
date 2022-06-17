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
        $file_name = $file->getClientOriginalName();//上传的文件名称
        $bucket = 'yaoliuyang-test-oss'; //bucket名称
        $object = $file_name;
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
                OssClient::OSS_STORAGE => OssClient::OSS_STORAGE_IA
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
