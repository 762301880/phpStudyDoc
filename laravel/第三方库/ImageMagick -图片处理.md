#  说明



## 资料

| 名称                              | 地址                                                        |
| --------------------------------- | ----------------------------------------------------------- |
| `ImageMagick `中文手册-第三方论坛 | [link](http://www.pooy.net/imagemagick-chinese-manual.html) |
| `php`官网-`Imagick`手册           | [link](https://www.php.net/manual/zh/book.imagick.php)      |
|                                   |                                                             |



# 安装

> 安装这里有待补占时先不做处理

# 代码实战

```php
     $image = new \Imagick($files->getRealPath());
     $image->setImageCompressionQuality(50);//设置图片压缩的质量
     //$image->cropThumbnailImage($image->getImageWidth()*0.5,$image->getImageHeight()*0.5);
     $image->writeImage($destinationPath . $fileName);//保存新的路径
```

