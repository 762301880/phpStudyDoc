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

    protected $accessKeyId = "LTAI4FqTWQmMztMtGzTX44Pr";
    protected $accessKeySecret = "ju1WFs5eX2nEVFSshb43myWuXWCSwj";
    protected $endpoint = "http://oss-cn-beijing.aliyuncs.com";

    public function upload(Request $request)
    {
        $file = $request->file('img');
        //$path      = $file->getPath() . '/' . $file->getFilename();//得到文件主机上的地址
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

```



