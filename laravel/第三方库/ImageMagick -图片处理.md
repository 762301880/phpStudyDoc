#  说明



## 资料

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `ImageMagick `中文手册-第三方论坛 | [link](http://www.pooy.net/imagemagick-chinese-manual.html)  |
| `php`官网-`Imagick`手册           | [link](https://www.php.net/manual/zh/book.imagick.php)       |
| 安装此 PECL 扩展                  | [link_one](https://www.php.net/manual/zh/imagick.installation.php)         [link_two](https://pecl.php.net/package/imagick) |
| imagemagick 官网                  | [link](http://www.imagemagick.org/script/install-source.php) |



# 安装-未完待续日后补充

## linux安装ImageMagick

**资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| linux-官网源码包 | [link ](http://www.imagemagick.org/script/install-source.php#linux)  [link](https://download.imagemagick.org/ImageMagick/download/) |
| 安装参考         | [link](https://www.it610.com/article/1292620735990276096.htm) |

**下载源码包&安装**

```shell
# 下载安装imagemagick
# linux 安装教程 http://www.imagemagick.org/script/install-source.php#linux
#wget https://download.imagemagick.org/ImageMagick/download/ImageMagick-7.1.0-18.tar.gz
wget https://download.imagemagick.org/ImageMagick/download/ImageMagick.tar.gz
# 解压
tar -zxvf ImageMagick.tar.gz
cd ImageMagick-7.1.0-18/
# 编译安装
./configure --prefix=/usr/local/imagemagick
make && make install

# 查询软件位置
find / -name convert 
/usr/local/imagemagick/bin/convert

# 验证是否安装成功
/usr/local/imagemagick/bin/convert --version
Version: ImageMagick 7.1.0-18 Q16-HDRI x86_64 2021-12-18 https://imagemagick.org
Copyright: (C) 1999-2021 ImageMagick Studio LLC
License: https://imagemagick.org/script/license.php
Features: Cipher DPC HDRI OpenMP(4.5) 
Delegates (built-in): lzma xml zlib
Compiler: gcc (8.5)

# 安装imagick
# 下载扩展包  http://pecl.php.net/package/imagick
wget http://pecl.php.net/get/imagick-3.6.0.tgz && tar -zxvf imagick-3.6.0.tgz && cd imagick-3.6.0
# 编译
[root@92201f5a71ce imagick-3.6.0]# find /  -name phpize
/usr/local/php7.4.3/bin/phpize
/root/php-7.4.3/scripts/phpize
[root@92201f5a71ce imagick-3.6.0]# /usr/local/php7.4.3/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
# 查询php-config位置
[root@92201f5a71ce imagick-3.6.0]# find / -name php-config
/usr/local/php7.4.3/bin/php-config
# 编译
[root@92201f5a71ce imagick-3.6.0]# ./configure --with-php-config=/usr/local/php7.4.3/bin/php-config  --with-imagick=/usr/local/imagemagick
[root@92201f5a71ce imagick-3.6.0]# make && make install
# 安装完成返回
Build complete.
Don't forget to run 'make test'.
Installing shared extensions:     /usr/local/php7.4.3/lib/php/extensions/no-debug-non-zts-20190902/
Installing header files:          /usr/local/php7.4.3/include/php/

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



