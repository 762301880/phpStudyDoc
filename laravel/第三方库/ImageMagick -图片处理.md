#  说明



## 资料

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `ImageMagick `中文手册-第三方论坛 | [link](http://www.pooy.net/imagemagick-chinese-manual.html)  |
| `php`官网-`Imagick`手册           | [link](https://www.php.net/manual/zh/book.imagick.php)       |
| 安装此 PECL 扩展                  | [link_one](https://www.php.net/manual/zh/imagick.installation.php)         [link_two](https://pecl.php.net/package/imagick) |



# 安装-未完待续日后补充

## linux安装ImageMagick

**资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| linux-官网源码包 | [link ](http://www.imagemagick.org/script/install-source.php#linux)  [link](https://download.imagemagick.org/ImageMagick/download/) |
| 安装参考         | [link](https://www.it610.com/article/1292620735990276096.htm) |

**下载源码包&安装**

```shell
# 下载源码包
wget https://download.imagemagick.org/ImageMagick/download/ImageMagick-7.1.0-18.tar.gz

# 解压
tar -zxvf ImageMagick-7.1.0-18.tar.gz

cd ImageMagick-7.1.0-18/
./configure --prefix=/usr/local/imagemagick
make
sudo make install
```



# 代码实战

```php
            $ext = $files->getClientOriginalExtension();//得到图片的文件后缀 如:jpg
            $destinationPath = 'uploads/xcx/' . date('Ymd') . '/';//构建新的保存路径
            $fileName = uniqid() . '.' . $ext;//构建唯一名称
            //todo 文件路径如果不存在不能自动创建问题
            if (!is_dir($destinationPath)){
                mkdir(public_path().'/'.($destinationPath), 0777, true);
            }
            $image=new \Imagick($files->getRealPath());
            $image->setImageCompressionQuality(50);//设置质量
            //$image->cropThumbnailImage($image->getImageWidth()*0.5,$image->getImageHeight()*0.5);//等比例重置大小
            $image->writeImage($destinationPath . $fileName);//保存到新路径    
```



