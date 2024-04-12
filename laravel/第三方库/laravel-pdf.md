#  一、说明与安装

## 说明

> wkhtmltopdf 和 wkhtmltoimage 是使用 QT Webkit 渲染引擎将 HTML 渲染为
>
>  PDF 和各种图像格式的命令行工具。这些完全“无头”运行，不需要显示或显示服务。

## 1.1资料

| 名称                                         | 地址                                                      |
| -------------------------------------------- | --------------------------------------------------------- |
| wkhtmltopdf官网                              | [链接](https://wkhtmltopdf.org/)                          |
| wkhtmltopdf软件下载地址                      | [链接](https://wkhtmltopdf.org/downloads.html)            |
| laravel-wkhtmltopdf扩展包 knplabs/knp-snappy | [链接](https://packagist.org/packages/knplabs/knp-snappy) |

## 1.2windows 使用`wkhtmltopdf`

###  1.2.1 windows 安装wkhtmltopdf

请在官网[下载](https://github.com/wkhtmltopdf/packaging/releases/download/0.12.6-1/wkhtmltox-0.12.6-1.msvc2015-win64.exe)`wkhtmltopdf`

![image-20210605171532987](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210605171532987.png)



### 1.2.2 配置环境变量

下载软件 [个人网盘分享](https://yaoliuyang.lanzoui.com/iA6wtptpqyj)

- 安装

> 安装就是无脑安装选择自己喜欢的路径即可	我的安装路径是`D:\wkhtmltopdf`

- 配置环境变量

> 安装之后需要配置环境变量才可以使用
>
> 点击 此电脑--鼠标右键选择属性--高级系统设置--环境变量

![image-20210605171938519](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210605171938519.png)

### 1.2.3 windows使用

- 使用cmd命令窗口到处网址为pdf

> 使用`wkhdmltopdf`命令将网址`https://www.baidu.com`也就是百度的首页打印导出到D盘名为`baidu.pdf`

```shell
wkhmltopdf https://www.baidu.com D:\baidu.pdf
```

- 结果示例
- ![image-20210605172520054](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20210605172520054.png)

## 1.3 linux安装`wkhtmltopdf`

### 1.3.1 linux安装依赖

注意力！请注意，您的系统上可能不存在某些依赖项（例如 libXrender），可能需要手动安装。

```php
apt-get install libXrender*
apt-get install libfontconfig*
```

> 先将二进制文件下载到vendor中，然后再移动到系统目录中，修改权限

```php
32位:
$ composer require h4cc / wkhtmltopdf-i386 0.12.x
$ composer require h4cc / wkhtmltoimage-i386 0.12.x，
64位:
$ composer require h4cc/wkhtmltopdf-amd64 0.12.x
$ composer require h4cc/wkhtmltoimage-amd64 0.12.x

(uname -a 命令查看系统位数)

cp vendor/h4cc/wkhtmltoimage-amd64/bin/wkhtmltoimage-amd64 /usr/local/bin/
cp vendor/h4cc/wkhtmltopdf-amd64/bin/wkhtmltopdf-amd64 /usr/local/bin/
并使其可执行：
chmod +x /usr/local/bin/wkhtmltoimage-amd64 
chmod +x /usr/local/bin/wkhtmltopdf-amd64
```

### 1.3.2 记得安装之配置依赖

> 在config\snappy.php中配置

```php
'binary'  => '/usr/local/bin/wkhtmltopdf-amd64',
```

#  二、laravel 使用`wkhdmltopdf`导出`html`为`pdf`

## 2.1安装扩展包

> 找了一圈发现packagist中有一个很完美的轮子我们拿过来直接用就可以了

- 安装（使用composer在项目的根目录中安装）

```shell
https://github.com/barryvdh/laravel-snappy
```

## 2.2 项目中使用

### 2.2.1 laravel 5.5以下的需要配置

- 在`config\app.php`中的`providers`数组中添加

```php
 'providers' => [
     Barryvdh\Snappy\ServiceProvider::class,
  ]
```

- 在`config\app.php`中的`aliases`数组中添加

```php
 'aliases' => [
     'PDF' => Barryvdh\Snappy\Facades\SnappyPdf::class,
     'SnappyImage' => Barryvdh\Snappy\Facades\SnappyImage::class,    
 ]
```

- 发布配置问件

```php
php artisan vendor:publish --provider="Barryvdh\Snappy\ServiceProvider"
```

### 2.2.2配置`config\snappy.php`依赖库

- 此配置文件 (config/snappy.php) 的主要更改将是二进制文件的路径。例如，当加载 Composer 时，该行应如下所示：

```php
'binary' => base_path('vendor/h4cc/wkhtmltopdf-amd64/bin/wkhtmltopdf-amd64'),
```

- 如果您遵循 vagrant 步骤，则该行应如下所示：

```php
'binary'  => '/usr/local/bin/wkhtmltopdf-amd64',
```

- 对于 Windows 用户，您必须在 wkhtmltopdf 的 bin 路径中添加双引号：

```php
'binary' => '"C:\Program Files\wkhtmltopdf\bin\wkhtmltopdf"'
```

## 2.3 基本使用

```php
use PDF;
# 根据链接生成pdf并保存到public目录下(save();方法保存服务器)
PDF::loadFile('https://www.baidu.com.com/')->save('baidu.pdf');
# 直接下载到本地(download();方法直接下载文件到本地)
return PDF::loadFile('https://www.baidu.com/')->download('bd.pdf');
# 显示在浏览器中(stream();方法直接在浏览器中显示 目前已淘汰, 目前使用inline();方法)
return PDF::loadFile('https://www.baidu.com/')->setOrientation('landscape')->stream('bd.pdf');
# 给模板传递变量并下载($name变量是一个数组,用来给模板渲染,welcome是模板类似于laravel blade模板)
$name=['name'=>'张三'];
return PDF::loadView('welcome',$name)->download('test.pdf');
```



# 待研究

