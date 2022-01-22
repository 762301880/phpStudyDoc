#  说明



## 资料

| 名称                              | 地址                                                         |
| --------------------------------- | ------------------------------------------------------------ |
| `ImageMagick `中文手册-第三方论坛 | [link](http://www.pooy.net/imagemagick-chinese-manual.html)  |
| `php`官网-`Imagick`手册           | [link](https://www.php.net/manual/zh/book.imagick.php)       |
| 安装此 PECL 扩展                  | [link_one](https://www.php.net/manual/zh/imagick.installation.php)         [link_two](https://pecl.php.net/package/imagick) |
| imagemagick 官网                  | [link](http://www.imagemagick.org/script/install-source.php) |

## linux安装ImageMagick

**资料**

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| linux-官网源码包 | [link ](http://www.imagemagick.org/script/install-source.php#linux)  [link](https://download.imagemagick.org/ImageMagick/download/) |
| 安装参考         | [link](https://www.it610.com/article/1292620735990276096.htm)   [link](安装-未完待续日后补充) |

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

# 最后找到php.ini文件再其中添加
# 或者指定绝对路径 extension='/usr/local/php7.4.3/lib/php/extensions/no-debug-non-zts-20190902/imagick.so'
extension=imagick.so
```

[**如果打印phpinfo()看不见imagick扩展解决方案**](https://blog.csdn.net/xiaofeizhumiss/article/details/106409062)

> 刷新一下php即可

<img src="https://s2.loli.net/2021/12/22/ParqNY8XtSegf4w.png" alt="1640155074(1).jpg" style="zoom:50%;" />

```shell
[root@92201f5a71ce php]# netstat -anp | grep 9000
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      113937/php-fpm: mas 
[root@92201f5a71ce php]# kill 113937
# 重新启动php-fpm
/usr/local/php7.4.3/sbin/php-fpm
```

**容器中测试php**

> 添加**/etc/nginx/conf.d/127.0.0.1.conf**

```shell
server {
    listen 80;
    server_name  127.0.0.1;
    root /usr/share/nginx/html;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;
    client_max_body_size 100m;
    charset utf-8;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        # fastcgi_pass unix:/var/run/php/php7.4-fpm.sock;
        fastcgi_pass  127.0.0.1:9000;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}

```



**查看扩展是否安装成功**

```shell
[root@92201f5a71ce etc]# php -m
[PHP Modules]
..........
imagick
..........
[Zend Modules]

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

**代码实战**

```php
 public function uploadImage(Request $request)
    {
        try {
            $file = $request->file('file');
            if (empty($file)) {
                throw new \ErrorException('上传图片不能为空');
            }
            # 验证上传图片格式
            if (!in_array($file->getClientOriginalExtension(), ['jpg', 'jpeg', 'png', 'bpm', 'gif', 'svg', 'webp'])) {
                throw new \ErrorException('上传文件必须是 jpg、jpeg、png、bpm、gif、svg、webp 其中的一种');
            }
            # 验证上传图片大小
            if (floor($file->getSize() / (1024 * 1024) > 10)) {
                throw new \ErrorException('上传文件必须保持在10M以内');
            }

            $file_name = uniqid() . '@' . $file->getClientOriginalName(); # 定义上传图片得唯一名称
            $uploadDirectory = '/uploads/' . date('Ymd');  #定义上传文件得路径
            $savePath = public_path($uploadDirectory);  # 定义上传绝对路径
            //文件路径如果不存在则自动创建 并赋予权限
            if (!is_dir($savePath)) {
                mkdir($savePath, 0777, true);
            }
            # 设置质量
            $image = new \Imagick($file->path());
            $image->setImageCompressionQuality(50);//设置图片压缩的质量
            $image->writeImage($savePath . '/' . $file_name);//保存新的路径
            # 缩放
            $thumb_file_name = uniqid() . '@' . '-thumb' . $file->getClientOriginalName(); #定义缩放的名称
            $image = new \Imagick($savePath . '/' . $file_name); # 实例化缩放的目标文件(这里必须是已经压缩过的图片)
            $image->cropThumbnailImage($image->getImageWidth() * 0.5, $image->getImageHeight() * 0.5); # 缩放操作
            $image->writeImage($savePath . '/' . $thumb_file_name);//保存新的路径
            // die();
            // # 上传图片逻辑
            // $file_name = uniqid() . '@' . $file->getClientOriginalName();
            // $uploadDirectory = '/uploads/' . date('Ymd');
            // $savePath = public_path($uploadDirectory);
            // $file->move($savePath . '/' . $file_name);
            if (file_exists($savePath . '/' . $file_name)) {
                $retData = [
                    'url' => url('/api/backend' . $uploadDirectory . '/' . $file_name), # 压缩过后的图片url地址
                    'relative_path' => $uploadDirectory . '/' . $file_name, # 压缩过后的图片的服务器相对地址
                    'thumb_image_url' => url('/api/backend' . $savePath . '/' . $thumb_file_name), # 压缩并缩放图片的url 地址
                    'thumb_relative_path' => $uploadDirectory . '/' . $thumb_file_name  # 压缩并缩放图片的相对地址
                ];
                return $this->success($retData, '图片上传成功');
            }
            return $this->failed('图片上传失败');
        } catch (\ErrorException $exception) {
            return $this->failed($exception->getMessage());
        }
    }
```

# bug解析

## [Laravel上传文件时getRealPath()返回public文件夹](https://blog.csdn.net/lkisgeek/article/details/120224083)

> 今天用**imageMagick**的时候发现**laravel的 $request->file('file')->getRealPath()**不管怎么传文件都会返回docker内部的项目目录**public**路径
>
> 无奈查询了一下这个函数用户的是**SPL**库,看了一下**php -m** 是有这个库的说明不是库的问题百度了一番发现是**php.ini**配置文件中的**upload_max_filesize** 限制比较小  于是我放开到**20M**,重启了一下php-fpm果然就可以返回我想要的临时文件

```php
find / -name php.ini
vim /usr/local/php7.4.3/etc/php.ini    
........
upload_max_filesize = 20M       # 修改大小 并wq保存   
........    
netstat -anp | grep 9000        # 查看php-fpm进程
tcp        0      0 127.0.0.1:9000          0.0.0.0:*               LISTEN      135/php-fpm: master
kil  135  # 关闭php-fpm
    
/usr/local/php7.4.3/sbin/php-fpm  # 重启php-fpm    
```

## [ImagickException: no decode delegate for this image format](https://blog.csdn.net/yangwenjie12/article/details/86644902?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1.pc_relevant_default&utm_relevant_index=1)

> 这个应该是说了**此图像格式没有解码委托**

**查看支持的格式**

```php
/usr/local/imagemagick/bin/convert   -list format  # 在显示的列表中查看有没有自己的格式
```

