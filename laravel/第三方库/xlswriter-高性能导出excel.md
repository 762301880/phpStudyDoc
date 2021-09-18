#  资料

| name                                                         | url                                                |
| ------------------------------------------------------------ | -------------------------------------------------- |
| [xlswriter](https://xlswriter.viest.me/index.html)高性能excel导出扩展 | [xlswriter](https://xlswriter.viest.me/index.html) |
| xlswriter-中文文档                                           | [link](https://xlswriter-docs.viest.me/zh-cn)      |
|                                                              |                                                    |

安装ide-提醒工具

```php
composer require viest/php-ext-xlswriter-ide-helper:dev-master
```

# 安装

## windows安装

### 下载**ddl**安装

| 下载名称                         | 下载地址                                                    |
| -------------------------------- | ----------------------------------------------------------- |
| GitHub Release-(github提供的ddl) | [link](https://github.com/viest/php-ext-xlswriter/releases) |
| PECL-(php官网提供的ddl)          | [link](https://pecl.php.net/package/xlswriter)              |
| php中文网-php安装扩展            | [link](https://www.php.cn/php-weizijiaocheng-392756.html)   |

- 我下载的是GitHub提供的ddl

  >php 版本不知道的可以**win+r**输入***cmd*** 输入***php -v***查看对应版本

  <img src="https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20210917144950885.png" alt="image-20210917144950885" style="zoom:50%;" />

- 下载之后配置

  > 然后将***php_xlswriter.dll***(我们只需要复制这个)，复制转移到php目录下的**ext目录中**
  >
  > 1. 本人采用的是集成环境所以复制到***D:\phpstudy_pro\Extensions\php\php7.4.3nts\ext***目录下，
  >
  > 2. 然后打开**php.ini**文件 大概在711行加入***extension=xlswriter***
  >
  > 3. 重启阿帕奇 
  > 4. 输入代码phpinfo(),打开看见网站的扩展有`xlswriter`即配置成功

# 导出

- 简单导出示例

```php
        $config = [
            //这里设置为导出到项目的public目录下
            'path' => public_path('/') // xlsx文件保存路径
        ];
        $excel = new \Vtiful\Kernel\Excel($config);
        /*
         * fileName 会自动创建一个工作表，
         * 你可以自定义该工作表名称，工作表名称为可选参数
         */
        $filePath = $excel->fileName('tutorial01.xlsx', 'sheet1')
            # 导出的标题
            ->header(['Item', 'Cost'])
            # 导出的数据
            ->data([
                ['Rent', 1000],
                ['Gas', 100],
                ['Food', 300],
                ['Gym', 50],
            ])
            ->output();
```

- 简单导入实验

```php
        $config = ['path' => public_path('/')];
        $excel = new \Vtiful\Kernel\Excel($config);
        // 读取测试文件
        // $file=$request->file('file')->getClientOriginalName();
        $data = $excel->openFile('tutorial01.xlsx')
            ->openSheet()
            ->getSheetData();
        dd($data); //返回数据表中的全部数据
# 通过表单上传
/**
 * 本来想着直接上传导入的看来不支持
 * 所以先上传到服务器然后再进行数据处理吧,
 * 如果真实开发环境需要弄一个定时任务每天删除
 * 上传的临时excel文件
 */
        $file_name=time().$request->file('file')->getClientOriginalName();# 设置上传的文件名称为了不重复设置了时间戳
        $date=date('Ymd');
        $path_name = public_path("/upload_temp_excel/{$date}");# 上传今天的文件夹
        $request->file('file')->move($path_name,$file_name);
        $config = ['path' => $path_name];
        # 判断文件是否上传成功
        if (!file_exists($path_name . '/' . $file_name)) {
            return response()->json(['code' => '5500', 'message' => '文件上传失败', 'data' => []]);
        }
        $excel = new \Vtiful\Kernel\Excel($config);
        // 读取测试文件
        $data = $excel->openFile($file_name)
            ->openSheet()
            ->getSheetData();
        var_dump($data); #得到读取的excel数据-这里忽略写入到数据库的逻辑
```

# 更多使用请参考官方文档-日后补充

