[^created_at:2021/12/13]

[^author:yaoliuyang]

#  说明

> 最近感觉舔狗日记挺有意思的很好玩，想着在群里发一下找找存在感，但是每天网上找一篇复制文字
>
> 太麻烦了，想着有没有api接口可以使用查询了一番果然有,但是问题又来了，看着网上基本上都是以
>
> 图片的形式去玩，既然api接口有了能不能使用代码将文字放在图片上然后我每次调用接口的时候直接
>
> 给你返回图片地址即可，好在php的GD库可以实现

## 资料

| 名称                    | 地址                                                         |
| ----------------------- | ------------------------------------------------------------ |
| php gd库                | [link](https://www.php.net/manual/zh/book.image.php)         |
| 免费的第三方舔狗日记api | [link](https://api.ixiaowai.cn/)  [link](https://www.tianapi.com/apiview/180) |
| ttf字体下载             | [link](https://www.aigei.com/font/?wd=ttf%E5%AD%97%E4%BD%93%E4%B8%8B%E8%BD%BD&bd_vid=8920790964488450245) |

## GD库简要介绍

> GD 库（也可以称为 GD2 函数库）是一个开源的用于创建图形图像的函数库，该函数库由C语言编写，可以在 Perl，PHP 等多种语言中使用。GD 库中提供了一系列用来处理图片的 API（接口），使用 GD 库可以处理图片、生成图片，也可以给图片加水印等。
>
> php -m 查看扩展有没有gd扩展即可没有请自行安装这里不提供安装方法

# 代码实例

## 代码参考

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 第三方博客参考 | [link](https://segmentfault.com/a/1190000006811582)  [link](https://www.cnblogs.com/wanghjun/p/9167985.html) |
|                |                                                              |



## [给图片添加文字](https://www.php.net/manual/zh/function.imagefttext.php)

> 主要采用了**gd库的imagefttext**函数实现

```php
$dst_path = 'dst.jpg'; # 这里传入图片的相对路径或者绝对路径地址
//创建图片的实例
$dst = imagecreatefromstring(file_get_contents($dst_path));

//打上文字
$font = './simsun.ttc';//字体路径

$black = imagecolorallocate($dst, 0x00, 0x00, 0x00);//字体颜色
imagefttext($dst, 13, 0, 20, 20, $black, $font, '快乐编程');
//输出图片
list($dst_w, $dst_h, $dst_type) = getimagesize($dst_path);
switch ($dst_type) {
    case 1://GIF
        header('Content-Type: image/gif'); # 输出gif图像到浏览器
        imagegif($dst);
        break;
    case 2://JPG
        header('Content-Type: image/jpeg'); # 输出jpeg图像到浏览器
        imagejpeg($dst);
        break;
    case 3://PNG
        header('Content-Type: image/png'); # 输出png图像到浏览器
        imagepng($dst);
        break;
    default:
        break;
}
imagedestroy($dst);
```

**laravel中使用示例**

图片素材下载：https://s2.loli.net/2021/12/13/jQlGcOzCHogwtv8.jpg

字体素材下载：https://www.aigei.com/font/?wd=ttf%E5%AD%97%E4%BD%93%E4%B8%8B%E8%BD%BD&bd_vid=8920790964488450245

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Support\Facades\DB;


class TestController extends Controller
{
    public function test(Request $request)
    {
        $dst_path = public_path('tiangou.jpg'); # 这里传入图片的相对路径或者绝对路径地址
        //创建图片的实例
        $dst = imagecreatefromstring(file_get_contents($dst_path));
        //打上文字
        $font = public_path('minikatong.ttf');//字体路径
        $outPutText = file_get_contents('https://api.ixiaowai.cn/tgrj/index.php');
        // $textBox = imagettfbbox(13, 0, $font, $outPutText);
        $fontText = "";
        for ($i = 0; $i <= mb_strlen($outPutText); $i += 16) {
            $fontText .= mb_substr($outPutText, $i, 16) . "\n";
        }
        $black = imagecolorallocate($dst, 0x00, 0x00, 0x00);//字体颜色
        imagefttext($dst, 13, 0, 10, 240, $black, $font, $fontText);
        list($dst_w, $dst_h, $dst_type) = getimagesize($dst_path);
        switch ($dst_type) {
            case 1://GIF
                header('Content-Type: image/gif'); # 输出gif图像到浏览器
                imagegif($dst);
                break;
            case 2://JPG
                header('Content-Type: image/jpeg'); # 输出jpeg图像到浏览器
                imagejpeg($dst);
                break;
            case 3://PNG
                header('Content-Type: image/png'); # 输出png图像到浏览器
                imagepng($dst);
                break;
            default:
                break;
        }
        imagedestroy($dst);
    }
}
```

**结果示例**

![res.jpg](https://s2.loli.net/2021/12/13/NsY9mjecKJuSaft.png)

