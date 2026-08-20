## accessKeyId与accessKeySecret 创建

> 创建**ram** 账户 并单独分配 **oss**相关权限 千万别直接给了总账户的 权限 

## 资料

| name                               | url                                                          |
| ---------------------------------- | ------------------------------------------------------------ |
| 阿里云文档中心-对象存储oss         | [link](https://help.aliyun.com/document_detail/85580.html)   |
| 查看 公共云下OSS各地域Endpoint如下 | [link](https://help.aliyun.com/document_detail/31837.htm?spm=a2c4g.11186623.0.0.605c273bVxKtaM#concept-zt4-cvy-5db) |

## 安装阿里云oss扩展

- 使用composer[安装](https://help.aliyun.com/document_detail/85580.html?spm=a2c4g.11186623.6.1006.6ea926fdpa6BHm)

```php
composer require aliyuncs/oss-sdk-php
```

## 代码示例

###  常用方法介绍

#### [PutObject](https://help.aliyun.com/zh/oss/developer-reference/putobject?scm=20140722.S_help%40%40%E6%96%87%E6%A1%A3%40%4031978._.ID_help%40%40%E6%96%87%E6%A1%A3%40%4031978-RL_put-LOC_doc%7EUND%7Eab-OR_ser-PAR1_212a5d4017808075948337959d7af4-V_4-PAR3_o-RE_new5-P0_0-P1_0&spm=a2c4g.11186623.help-search.i20)  （上传**文件内容 / 数据流**）

**代码示例**

```php
$ossClient->putObject($this->bucket, $object, $content);
```



#### uploadFile    (上传**本地已存在的文件**)

**代码示例**

```php
$ossClient->uploadFile($bucket, $object, "D:\\localpath\\exampleobject.jpg");
```



---



###  上传示例(参考示例 不可做正式代码)

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

namespace App\Services;

use OSS\Core\OssException;
use OSS\OssClient;

class AliOssService
{
    protected $accessKeyId;
    protected $accessKeySecret;
    protected $bucket;
    protected $endpoint;

    // 缓存OSS客户端实例，全局复用
    private ?OssClient $ossClient = null;

    // 直接从 .env 读取，不经过任何配置文件
    public function __construct()
    {
        $this->accessKeyId     = env('ALI_OSS_ACCESS_KEY_ID');
        $this->accessKeySecret = env('ALI_OSS_ACCESS_KEY_SECRET');
        $this->bucket          = env('ALI_OSS_BUCKET');
        $this->endpoint        = env('ALI_OSS_ENDPOINT');

        // 提前校验配置完整性，避免运行时报错
        if (empty($this->accessKeyId) || empty($this->accessKeySecret) || empty($this->bucket) || empty($this->endpoint)) {
            throw new \Exception('阿里云OSS环境变量配置缺失，请检查.env文件');
        }
    }

    /**
     * 统一获取OSS客户端，单例复用，只实例化一次
     * @return OssClient
     */
    private function getOssClient(): OssClient
    {
        if ($this->ossClient === null) {
            $this->ossClient = new OssClient(
                $this->accessKeyId,
                $this->accessKeySecret,
                $this->endpoint
            );
            // 可选超时配置优化请求
            $this->ossClient->setConnectTimeout(3);
            $this->ossClient->setTimeout(10);    
        }
        return $this->ossClient;
    }

    /**
     * 上传图片（推荐使用，自动生成文件名）
     * @param $file 上传的文件 $request->file('img')
     * @param string $dir 上传目录，例如 'avatar' 'product'
     * @return array
     * @throws \Exception
     */
    public function uploadImage($file, string $dir = 'uploads')
    {
        if (empty($file) || !$file->isValid()) {
            throw new \Exception('图片上传无效');
        }

        // 生成唯一文件名
        $extension = $file->getClientOriginalExtension();
        $fileName = uniqid() . '_' . date('YmdHis') . '.' . $extension;
        $object = $dir . '/' . $fileName; // OSS 里的文件路径（存数据库）
        $originalName = $file->getClientOriginalName();

        try {
            // 复用单例客户端
            $ossClient = $this->getOssClient();

            // 上传文件流
            $content = file_get_contents($file->getPathname());
            $ossClient->putObject($this->bucket, $object, $content);

            // 生成私有访问签名URL（1小时有效）
            $signedUrl = $ossClient->signUrl($this->bucket, $object, 3600);

            // 返回标准结构（生产级）
            return [
                'object' => $object,          // OSS文件路径 → 存数据库
                'signed_url' => $signedUrl,    // 临时签名URL → 前端显示
                'original_name' => $originalName, // 原始文件名
            ];

        } catch (OssException $e) {
            throw new \Exception("OSS上传失败：" . $e->getMessage());
        }
    }

    /**
     * 根据 OSS 路径 object 获取签名URL  私有bucket需要返回加密url
     * @param string $object
     * @param int $expires
     * @return string
     * @throws OssException
     */
    public function getSignedUrl(string $object, int $expires = 3600): string
    {
        $ossClient = $this->getOssClient();
        return $ossClient->signUrl($this->bucket, $object, $expires);
    }

    /**
     * 上传文本内容
     * 如何上传本地内容 content 传递 file_get_content(本地地址)
     * @param string $content 文字内容
     * @param string $fileName
     * @param string $dir
     * @return string oss文件路径object
     * @throws \Exception
     * @throws OssException
     */
    public function uploadContent(string $content, string $fileName, string $dir = 'uploads'): string
    {
        $ossClient = $this->getOssClient();
        $object = $dir . '/' . $fileName;
        $result = $ossClient->putObject($this->bucket, $object, $content);
        $code = $result["info"]["http_code"] ?? 0;
        if ($code !== 200) {
            throw new \Exception("上传失败，请检查OSS配置或文件权限");
        }
        return $object;
    }
    
     /**
     * 头像缩略压缩签名链接（阿里云官方标准写法，兼容所有V1 PHP SDK）
     * @param string $object oss文件路径
     * @param int $expires 链接有效期 秒
     * @param int $width 头像宽度
     * @return string
     */
    public function getAvatarSignedUrl(string $object, int $expires = 3600 * 24 * 30, int $width = 120): string
    {
        if (empty($object)) {
            return '';
        }
        // 和原有逻辑保持一致：去除路径开头斜杠
        if (strpos($object, '/') === 0) {
            $object = ltrim($object, '/');
        }

        $ossClient = $this->getOssClient();

        // 官方固定key：OSS_PROCESS 专门用来传x-oss-process图像处理指令
        $options = [
            \OSS\OssClient::OSS_PROCESS => "image/resize,w_{$width}/format,webp/quality,q_70"
        ];

        // 严格对齐你SDK定义的5个参数顺序
        return $ossClient->signUrl(
            $this->bucket,
            $object,
            $expires,
            \OSS\OssClient::OSS_HTTP_GET,
            $options
        );
    }
    
    public function getEndpoint()
    {
        return $this->endpoint;
    }

    public function getBucket()
    {
        return $this->bucket;
    }

    /**
     * 拷贝文件
     * @param $path
     * @param $toPath
     * @return null
     * @throws OssException
     * @throws \OSS\Http\RequestCore_Exception
     */
    public function copyObject($path, $toPath)
    {
        $ossClient = $this->getOssClient();
        // 第一步：判断源文件是否存在OSS
        $isExists = $this->ossClient->doesObjectExist($this->bucket, $path);
        $toPathIsExists = $this->ossClient->doesObjectExist($this->bucket, $toPath);
        if ($toPathIsExists) return $toPath;//如果上传文件已经存在正式目录直接返回
        if (!$isExists) throw new \Exception('OSS源文件不存在，无法复制');
        $ossClient->copyObject($this->bucket, $path, $this->bucket, $toPath);
        return $toPath;
    }
}
```



### 创建储存空间

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

## 问题示例

### oss配置生命周期

**需要删除的图片**

> 由图片可见我们需要删除的图片未知在**temp临时目录下**

![1655685898492.jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1R3UpWJQor6eG28.png)

**配置生命周日**

> 点击左侧导航栏的**基础设置-生命周期** <font color="color=#dddd00">**创建规则**</font>  配置匹配的前缀(**可以通过点击图片查看链接查看前缀**),然后设置**文件过期策略删除**即可

![1655685309610.jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/lgOmPr7GVqCNYFs.png)

### oss配置生命周期未生效原因

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

## 跨域资源共享](https://help.aliyun.com/document_detail/32110.html)

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

## oss客户端

### oss browser

> [下载](https://oss.console.aliyun.com/services/tools)

![image-20240412141557217](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412141557217.png)

![image-20240412142052985](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20240412142052985.png)

## 重要事情记录(超级重要必看)

<font color='red'>记住千万不要用超级用户的accessKey会产生盗号非法创建资源风险  20240411被人盗用超级账户的accesskey 之后开通了几百个ECL实例产生了大量费用
</font>

## bug解析

### 线上域名请求oss报错CORS跨域

登录阿里云 → 打开「对象存储 OSS」

左侧「Bucket 列表」，点开你的桶：`vibetv-short-play`

左侧侧边栏找到 **数据安全**（旧版叫权限管理）

右侧页面往下滑，找到 **跨域设置**，点【设置】→【创建规则】

来源 Origin： 设置线上域名**https://xxxxx.com**

## 封装上传到临时目录 提交转移到正式目录(正式环境可用)

### AliOssService代码

```php
<?php

namespace App\Services;

use App\Services\Abs\CommonService;
use OSS\Core\OssException;
use OSS\OssClient;

class AliOssService extends CommonService
{
    protected $accessKeyId;
    protected $accessKeySecret;
    protected $bucket;
    protected $endpoint;
    private ?OssClient $ossClient = null;


    // 直接从 .env 读取，不经过任何配置文件
    public function __construct()
    {
        $this->accessKeyId = env('ALI_OSS_ACCESS_KEY_ID');
        $this->accessKeySecret = env('ALI_OSS_ACCESS_KEY_SECRET');
        $this->bucket = env('ALI_OSS_BUCKET');
        $this->endpoint = env('ALI_OSS_ENDPOINT');
    }


    /**
     * 统一获取OSS客户端，单例复用，只实例化一次
     * @return OssClient
     */
    private function getOssClient(): OssClient
    {
        if ($this->ossClient === null) {
            $this->ossClient = new OssClient(
                $this->accessKeyId,
                $this->accessKeySecret,
                $this->endpoint
            );
            // 可选超时配置优化请求
            $this->ossClient->setConnectTimeout(3);
            $this->ossClient->setTimeout(10);
        }
        return $this->ossClient;
    }

    public function uploadContent(string $content, string $fileName, string $dir = 'uploads'): string
    {
        $ossClient = $this->getOssClient();
        $object = $dir . '/' . $fileName;
        $result = $ossClient->putObject($this->bucket, $object, $content);
        $code = $result["info"]["http_code"] ?? 0;
        if ($code !== 200) {
            throw new \Exception("上传失败，请检查OSS配置或文件权限");
        }
        return $object;
    }

    /**
     * 上传文件到OSS temp临时目录
     * @param $file Illuminate\Http\UploadedFile
     * @param string $savePath
     * @return array
     * @throws \Exception
     */
    public function uploadFileToTemp($file, $savePath = 'temp')
    {
        if (empty($file) || !$file->isValid()) {
            throw new \Exception('上传文件无效');
        }
        $yearDate = date("Ymd");
        $ossPrefix = $savePath . "/" . $yearDate;
        $extension = $file->getClientOriginalExtension();
        $safeFileName = uniqid() . '_' . date('YmdHis') . '.' . $extension;
        $ossKey = $ossPrefix . "/" . $safeFileName;
        $ossClient = $this->getOssClient();
        $bucket = $this->bucket;
        try {
            $localRealPath = $file->getPathname();
            $ossClient->uploadFile($bucket, $ossKey, $localRealPath);
        } catch (OssException $e) {
            throw new \Exception("OSS上传失败：" . $e->getMessage());
        }
        // 生成短期预览签名，仅用于表单页面预览，禁止入库
        $previewSignedUrl = $this->getSignedUrl($ossKey, 1800);

        return [
            'oss_key' => $ossKey,
            'preview_signed_url' => $previewSignedUrl,
            'original_name' => $file->getClientOriginalName(),
        ];
    }


    /**
     * OSS临时文件迁移至正式目录（内网复制，无流量损耗）
     * $tempOssKey temp/20260729/6a6974b992cd2.20260729113417@c3b73dd979ecd0e784114b7675ae067a.jpeg
     */
    /**
     * 将前端传回的完整OSS签名URL，解析后从temp临时目录拷贝到正式目录
     * @param string $url 前端传过来完整带域名+签名的preview_signed_url
     * @param string $dir 正式目录，例：upload/article
     * @return string 返回正式oss_key（存入数据库）
     * @throws \Exception
     */
    public function copyOssTempToFormal($url, $dir)
    {

        $bucket = $this->bucket;
        $ossClient = $this->getOssClient();

        // 1.解析URL，提取path，丢弃域名、?后面所有签名参数
        $parts = parse_url($url);
        if (!$parts || empty($parts['path'])) {
            throw new \Exception('OSS资源URL解析失败');
        }
        // 去除路径开头斜杠，得到纯净object key
        $ossKey = ltrim($parts['path'], '/');
        //正式目录#############
        $arr = explode('/', $ossKey);
        $fileName = end($arr);
        $dirFileName = $dir . "/" . $fileName; //需要转移的文件
        ######################
        //判断是否存在正式目录中如果存在直接返回(编辑情况)
        if ($ossClient->doesObjectExist($bucket, $dirFileName)) {
            return $dirFileName;
        }
        // 2.安全校验：只允许 temp/ 临时目录，防止越权
        if (strpos($ossKey, 'temp/') !== 0) {
            throw new \Exception('仅允许迁移temp临时目录资源');
        }
        // 3.OSS远程校验文件真实存在（不可省略，防前端伪造路径）
        if (!$ossClient->doesObjectExist($bucket, $ossKey)) {
            throw new \Exception('临时文件不存在或已被OSS生命周期删除');
        }
        // 5.OSS服务端拷贝，文件不会下载经过PHP服务器
        $ossClient->copyObject($bucket, $ossKey, $bucket, $dirFileName);
        return $dirFileName;
    }

    /**
     * 根据 OSS 路径 object 获取签名URL
     * @param string $object
     * @param int $expires
     * @return string
     * @throws OssException
     */
    public function getSignedUrl(string $object, int $expires = 3600)
    {
        $ossClient = $this->getOssClient();
        return $ossClient->signUrl($this->bucket, $object, $expires);
    }
}
```

### FileUploadService 代码

> 以下代码是干嘛用的 
>
> 是给php后端代码调用的 例如**getFullUrls** 从库里得到的路径我需要转化为oss全路径

```php
<?php

namespace App\Services;

use App\Services\Abs\CommonService;

class FileUploadService extends CommonService
{
    protected $oss = null;

    public function __construct()
    {
        $this->oss = AliOssService::getInstance();
    }

    /**
     * 获取url对应的oss路径
     * 会自动判断 如果在临时目录转移到正式目录  如果正式目录有的情况下直接返回
     * dir 不可以 / 开头
     * @param $url
     * @param $dir
     * @return array|string
     */
    public function getFullUrlPath($url, $dir)
    {
        if (empty($dir)) throw new \Exception("需要保存的图片地址不能为空");
        if (empty($url)) return $url;
        $retArr = [];
        if (is_array($url)) {
            foreach ($url as $key => $v) {
                $retArr[$key] = $this->oss->copyOssTempToFormal($v, $dir);
            }
            return $retArr;
        }
        //不是数组情况下
        return $this->oss->copyOssTempToFormal($url, $dir);
    }

    /**
     * 数据库里的地址转化为oss地址
     * @param $file_path
     * @return array|string
     * @throws \OSS\Core\OssException
     */
    public function getFullUrls($file_path)
    {
        if (empty($file_path)) return $file_path;
        $retArr = [];
        if (is_array($file_path)) {
            foreach ($file_path as $key => $file) {
                $retArr[$key] = $this->oss->getSignedUrl($file);
            }
            return $retArr;
        }
        return $this->oss->getSignedUrl($file_path);
    }
}
```

### FileUpload控制器代码(对外暴露上传端口)

**路由**

```php
Route::group(['prefix' => 'file'], function () {
    Route::post('uploadImg',[\App\Http\Controllers\FileUpload::class, 'uploadImg']);//上传图片
    //上传视频
});
```

**控制器**

```php
<?php

namespace App\Http\Controllers;

use App\Services\AliOssService;
use App\Traits\ApplyResponseLayout;
use Illuminate\Http\Request;
use Illuminate\Support\Collection;

class FileUpload extends Collection
{
    use ApplyResponseLayout;

    protected $oss = null;

    public function __construct($items = [])
    {
        parent::__construct($items);
        $this->oss = AliOssService::getInstance();
    }

    public function uploadImg(Request $request)
    {
        $file = $request->file('file');

        try {
            if (empty($file)) throw new \ErrorException('上传图片不能为空');
            # 验证上传图片格式
            $filter_suffix = ['jpg', 'jpeg', 'png', 'bpm', 'gif', 'svg', 'webp'];
            if (!in_array($file->getClientOriginalExtension(), $filter_suffix)) {
                $suffix = implode('、', $filter_suffix);
                throw new \ErrorException("上传文件必须是 {$suffix} 其中的一种");
            }
            # 验证上传图片大小
            if (floor($file->getSize() / (1024 * 1024) > 3)) throw new \ErrorException('上传文件必须保持在3M以内');
            $upload = $this->oss->uploadFileToTemp($file);
        } catch (\Exception $e) {
            return $this->error($e->getMessage());
        }
        return $this->success('图片上传成功', $upload);
    }
}
```

