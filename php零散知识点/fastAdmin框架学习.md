# 资料

| 名称 | 地址                                            |
| ---- | ----------------------------------------------- |
| 官网 | [link](https://www.fastadmin.net/download.html) |

# 环境搭建

##  linux_nginx配置

**添加虚拟域名**

> **/etc/hosts** 中添加

```shell
# 自定义
127.0.0.1  www.fastadmin.com
```

**添加nginx配置文件**

> 在**/etc/nginx/sites-enabled**目录下添加**www.fastadmin.com**

```shell

yly@yly-pc:/etc/nginx/sites-enabled$ cat www.fastadmin.com 
server {
    listen 80;
    server_name www.fastadmin.com;
    root /home/yly/Documents/work/fastadmin/public/;

    add_header X-Frame-Options "SAMEORIGIN";
    add_header X-XSS-Protection "1; mode=block";
    add_header X-Content-Type-Options "nosniff";

    index index.php;

    charset utf-8;

 
 
 
   # 转发配置
   location ~* (runtime|application)/{
	return 403;
}
# ilBXVmAQYs.php 修改为安装之后public目录下生成的对应php文件
location ^~ /ilBXVmAQYs.php/ {   
	if (!-e $request_filename){
    	rewrite  ^\/ilBXVmAQYs\.php(.*)$  /ilBXVmAQYs.php?s=$1 last;   break;
    }
} 
location / {
	if (!-e $request_filename){
		rewrite  ^(.*)$  /index.php?s=$1  last;   break;
	}
}
   
   # 
 
 
 
 

    location = /favicon.ico { access_log off; log_not_found off; }
    location = /robots.txt  { access_log off; log_not_found off; }

    error_page 404 /index.php;

    location ~ \.php$ {
        fastcgi_pass unix:/var/run/php/php7.3-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $realpath_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.(?!well-known).* {
        deny all;
    }
}
```

**bug解析**

遇到nginx错误问题 请查看** cat /var/log/nginx/error.log**

一般都是目录权限问题

## 安装

> 域名/install.php执行安装

```php
www.fastadmin.com/install.php
```

**自动生成的数据表**

![image-20231122101548216](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231122101548216.png)

#  bug解析

## fastadmin部署出现后台登录404[后台不能伪静态的处理方式

**参考**

| name     | url                                                          |
| -------- | ------------------------------------------------------------ |
| 参考博客 | [link](https://huaweidevelopers.csdn.net/6507bcece0aa6850f5a232e6.html) |

> 今天部署fastadmin程序的时候后台登录界面死活是404
> http://127.0.0.1/JEKSecoHgV.php
> 自动跳转到http://127.0.0.1/JEKSecoHgV.php/index/login
> 然后404
>
> 
>
> 测试
> http://127.0.0.1/JEKSecoHgV.php?s=/index/login
> 成功打开
> 估计是伪静态的问题
>
> fastadmin默认部署推荐的是thinkphp伪静态
>
> 今天部署fastadmin程序的时候后台登录界面死活是404
> http://127.0.0.1/JEKSecoHgV.php
> 自动跳转到http://127.0.0.1/JEKSecoHgV.php/index/login
> 然后404
>
> 
>
> 测试
> http://127.0.0.1/JEKSecoHgV.php?s=/index/login
> 成功打开
> 估计是伪静态的问题
>
> fastadmin默认部署推荐的是thinkphp伪静态



**解决代码**

> nginx配置文件中添加
>
> 中间似乎没有针对后台页面JEKSecoHgV.php的伪静态，但是在一些宝塔下可以正常运行（不知道什么原因）。很不幸，我今天碰到了不能正常运行的宝塔，无奈尝试自己写后台页面的伪静态。

```shell
location ~* (runtime|application)/{
	return 403;
}
location ^~ /JEKSecoHgV.php/ {  
	if (!-e $request_filename){
    	rewrite  ^\/JEKSecoHgV\.php(.*)$  /JEKSecoHgV.php?s=$1 last;   break;
    }
} 
location / {
	if (!-e $request_filename){
		rewrite  ^(.*)$  /index.php?s=$1  last;   break;
	}
}
```

##  后台登录成功一直不跳转

**资料**

| name     | url                                                   |
| -------- | ----------------------------------------------------- |
| 参考博客 | [link](https://ask.fastadmin.net/question/30972.html) |

> 因为后台已经可以打开了，我基本已经排查了伪静态问题，但是一直让我登录，不让我进入就很奇怪，看了源码，怀疑是session问题。登录的时候session文件已经生成了，没有问题，但是账号密码输入之后session没有任何改变，说明这一步操作的时候session写入出错了，继续排查在Auth.php约193-198行\

```php
if (Config::get('fastadmin.loginip_check')) {
            if (!isset($admin['loginip']) || $admin['loginip'] != request()->ip()) {
                $this->logout();
                return false;
            }
        }
```

我不知道我的ip为什么代码检测会一直变动，很奇怪，所以我把配置文件的

```php
//是否开启IP变动检测
'loginip_check'         => false,
```

##  调用插件返回报错 404

![image-20251108090148387](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20251108090148387.png)

**windows处理**

> D:\phpstudy_pro\Extensions\Nginx1.15.11\conf\vhosts
>
> nginx配置文件中新增注释部分   
>
> 然后重启nginx

```nginx
server {
        listen        80;
        server_name  www.hqmedia.com;
        root   "D:/phpstudy_pro/WWW/work/hq_media/public";
        location / {
            index index.php index.html error/index.html;
            error_page 400 /error/400.html;
            error_page 403 /error/403.html;
            error_page 404 /error/404.html;
            error_page 500 /error/500.html;
            error_page 501 /error/501.html;
            error_page 502 /error/502.html;
            error_page 503 /error/503.html;
            error_page 504 /error/504.html;
            error_page 505 /error/505.html;
            error_page 506 /error/506.html;
            error_page 507 /error/507.html;
            error_page 509 /error/509.html;
            error_page 510 /error/510.html;
            include D:/phpstudy_pro/WWW/work/hq_media/public/nginx.htaccess;
            autoindex  off;
            # 处理静态文件
            if (!-e $request_filename){
               rewrite  ^(.*)$  /index.php?s=$1  last;   break;
            }
        }
        location ~ \.php(.*)$ {
            fastcgi_pass   127.0.0.1:9005;
            fastcgi_index  index.php;
            fastcgi_split_path_info  ^((?U).+\.php)(/?.+)$;
            fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
            fastcgi_param  PATH_INFO  $fastcgi_path_info;
            fastcgi_param  PATH_TRANSLATED  $document_root$fastcgi_path_info;
            include        fastcgi_params;
        }
}
```

# 插件使用

**破解插件下载**

| 名称  | 地址                                                         |
| ----- | ------------------------------------------------------------ |
| gitee | [下载地址](https://gitee.com/arife/fastadmin-plugin-arife/repository/archive/master.zip) |



## 富文本

> 下载插件后  添加class   class="form-control editor Summernote"

```php+HTML
 <div class="col-xs-12 col-sm-8">
            <textarea id="c-content" class="form-control editor Summernote" rows="5" name="row[content]"
                      cols="50"></textarea>
        </div>
```

## 经纬度 获取



```html
    <div class="form-group">
        <label class="control-label col-xs-12 col-sm-2">{:__('Lat')}:</label>
        <div class="col-xs-12 col-sm-8">
            <input id="c-lat" class="form-control" name="row[lat]" type="text">
        </div>
    </div>
    <div class="form-group">
        <label class="control-label col-xs-12 col-sm-2">{:__('Lng')}:</label>
        <div class="col-xs-12 col-sm-8">
            <input id="c-lng" class="form-control" name="row[lng]" type="text">
        </div>
    </div>
    <div class="form-group">
        <label class="control-label col-xs-12 col-sm-2">{:__('Address')}:</label>
        <div class="col-xs-12 col-sm-8">
            <input id="c-address" class="form-control" name="row[address]" type="text">
            <button type="button" class="btn btn-primary" data-toggle="addresspicker" data-input-id="c-address" data-lng-id="c-lng" data-lat-id="c-lat">点击获取经纬度</button>
        </div>
    </div>
```

## 在线命令管理

![image-20251108093505458](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20251108093505458.png)

#  补充

# fastadmin  列表 按钮开关怎么弄

![image-20251108101822608](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20251108101822608.png)

在 **FastAdmin** 里，列表（即 `Table`）的按钮开关一般是用 `Table.api.formatter.toggle` 来实现的。这个开关按钮可以一键启用/禁用、显示/隐藏、上下架等，非常常用。下面是详细说明和完整示例👇

------

## ✅ 一、功能概览

在 FastAdmin 的 `bootstrapTable` 中，想在某一列显示一个开关按钮（Switch），只要设置：

```
formatter: Table.api.formatter.toggle
```

即可自动生成一个可以点击切换状态的开关。

------

## ✅ 二、使用示例

假设我们有一张项目表 `project`，字段有 `status`（状态字段，1=启用，0=禁用）：

### JS 文件（`project.js`）

```
define(['jquery', 'bootstrap', 'backend', 'table', 'form'], function ($, undefined, Backend, Table, Form) {

    var Controller = {
        index: function () {
            // 初始化表格参数配置
            Table.api.init({
                extend: {
                    index_url: 'project/index' + location.search,
                    toggle_url: 'ajax/toggle', // 通用切换接口
                    table: 'project',
                }
            });

            var table = $("#table");

            // 初始化表格
            table.bootstrapTable({
                url: $.fn.bootstrapTable.defaults.extend.index_url,
                pk: 'id',
                sortName: 'id',
                columns: [[
                    {checkbox: true},
                    {field: 'id', title: 'ID'},
                    {field: 'name', title: '项目名称'},
                    {
                        field: 'status',
                        title: '状态',
                        searchList: {"1": '启用', "0": '禁用'},
                        formatter: Table.api.formatter.toggle, // ✅ 开关按钮
                    },
                    {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
                ]]
            });

            // 绑定事件
            Table.api.bindevent(table);
        },
        add: function () {
            Controller.api.bindevent();
        },
        edit: function () {
            Controller.api.bindevent();
        },
        api: {
            bindevent: function () {
                Form.api.bindevent($("form[role=form]"));
            }
        }
    };

    return Controller;
});
```

------

## ✅ 三、后端控制器支持

FastAdmin 已内置通用的 `ajax/toggle` 接口（即 `application/admin/controller/Ajax.php` 中的 `toggle()` 方法）。

如果你表中的 `status` 字段是 `tinyint(1)` 或 `enum('0','1')`，默认就能用，无需额外写接口。
 只要在 JS 里设置：

```
extend: {
    toggle_url: 'ajax/toggle',
}
```

后台会根据主键和字段名自动修改对应值。

------

## ✅ 四、自定义开关样式（可选）

可以在后台设置 `toggleStyle`，或者使用自定义 formatter，比如：

```
formatter: function (value, row, index) {
    var color = value == 1 ? 'success' : 'danger';
    var text = value == 1 ? '启用' : '禁用';
    return '<a href="javascript:;" class="btn btn-xs btn-' + color + ' btn-toggle" data-id="' + row.id + '" data-field="status">' + text + '</a>';
}
```

然后监听点击事件进行 AJAX 请求。

------

## ✅ 五、常见问题

| 问题                 | 原因                   | 解决方法                                                 |
| -------------------- | ---------------------- | -------------------------------------------------------- |
| 点击无反应           | 没引入 `toggle_url`    | 在 `Table.api.init()` 里添加 `toggle_url: 'ajax/toggle'` |
| 提示“此功能未被授权” | 插件未安装或接口被禁用 | 检查是否关闭了 Ajax 控制器的权限                         |
| 切换后页面不刷新     | 未绑定事件             | 确保最后执行 `Table.api.bindevent(table);`               |

# 图片上传

![image-20251108104421883](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20251108104421883.png)

**对应数据表展示**

```sql
CREATE TABLE `hq_company_info` (
  `id` int(11) NOT NULL AUTO_INCREMENT,
  `img` varchar(255) DEFAULT NULL COMMENT '介绍图片',
  `text` text COMMENT '介绍文本',
  `create_time` datetime DEFAULT NULL,
  `update_time` varchar(255) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='企业介绍';
```

## 一、准备：在后台生成 CURD

在 FastAdmin 后台：

> 开发管理 → 代码生成器 → 生成后台管理

填写以下内容：

| 配置项   | 填写示例                           |
| -------- | ---------------------------------- |
| 表名     | `hq_company_info`                  |
| 控制器名 | `hq/company_info`                  |
| 菜单名称 | 企业介绍                           |
| 主键     | `id`                               |
| 模型名   | `app\admin\model\hq\CompanyInfo`   |
| 生成选项 | ✅ 生成控制器、模型、视图、JS、菜单 |

点击“生成”。

## ✅ 二、生成后进行手动增强

FastAdmin 生成的 CURD 默认只支持普通文本，我们要让：

- `img` 字段 → 图片上传
- `text` 字段 → 富文本编辑器

下面分别改这两部分。

------

## ✅ 三、修改 `hq_company_info.js`

文件路径：
 `/public/assets/js/backend/hq/company_info.js`

找到表格定义 `columns: [[ ... ]]` 的部分，修改如下：

```
columns: [[
    {checkbox: true},
    {field: 'id', title: __('Id')},
    {
        field: 'img',
        title: __('Img'),
        formatter: Table.api.formatter.image, // ✅ 显示为图片
        operate: false
    },
    {
        field: 'text',
        title: __('Text'),
        operate: false,
        formatter: function (value) {
            // ✅ 截取部分内容预览
            return value ? value.substring(0, 50) + '...' : '';
        }
    },
    {field: 'create_time', title: __('Create_time'), operate:'RANGE', addclass:'datetimerange'},
    {field: 'update_time', title: __('Update_time')},
    {field: 'operate', title: __('Operate'), table: table, events: Table.api.events.operate, formatter: Table.api.formatter.operate}
]]
```

------

## ✅ 四、修改 `add.html` 与 `edit.html`

路径：

```
/application/admin/view/hq/company_info/add.html  
/application/admin/view/hq/company_info/edit.html
```

找到表单部分，替换对应字段：

### 图片上传

```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">介绍图片:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="input-group">
            <input id="c-img" class="form-control" name="row[img]" type="text">
            <div class="input-group-addon no-border no-padding">
                <span><button type="button" id="plupload-img" class="btn btn-danger plupload" data-input-id="c-img" data-preview-id="p-img"><i class="fa fa-upload"></i> 上传</button></span>
            </div>
            <span class="msg-box n-right"></span>
        </div>
        <ul class="row list-inline plupload-preview" id="p-img"></ul>
    </div>
</div>
```

### 富文本编辑器

```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">介绍文本:</label>
    <div class="col-xs-12 col-sm-8">
        <textarea id="c-text" class="form-control editor" name="row[text]" rows="10"></textarea>
    </div>
</div>
```

------

## ✅ 五、JS 启用上传与编辑器

在 `add` 和 `edit` 页面对应的 JS 中（`Controller.api.bindevent()` 方法中），FastAdmin 默认已经绑定：

```javascript
Form.api.bindevent($("form[role=form]"));
```

这行代码自动处理：

- `.plupload` 按钮 → 上传图片（使用 FastAdmin 自带上传组件）
- `.editor` → 富文本（内置 Summernote 编辑器）

所以不需要手动初始化。
 直接保存就能用了 ✅

------

## ✅ 六、效果预览

后台页面：

| 字段   | 效果                               |
| ------ | ---------------------------------- |
| img    | 上传图片按钮 + 缩略图预览          |
| text   | 富文本编辑框                       |
| 列表页 | 图片显示缩略图、文本显示前50字预览 |

------

## ✅ 七、可选：限制上传图片类型

如果只想上传图片文件，可以在按钮上加上属性：

```
data-mimetype="image/gif,image/jpeg,image/png,image/jpg"
```

------

是否要我帮你把完整生成后的 `company_info.js` 文件示例写好（含表格、上传、富文本全部完善）？
 我可以直接生成你粘贴即可用的版本。

##### 你说：

编辑没有回显图片

###### ChatGPT 说：

非常好的问题 👍
 👉 **FastAdmin 编辑页图片不回显** 是一个常见的小坑。
 其实上传功能是好的，只是 **编辑页没正确显示上传图片的预览**。

下面我详细教你如何修好它（完全可复制粘贴版）👇

------

## ✅ 一、确认你使用的上传表单代码

确保你的编辑页 `edit.html`（或 add.html）中 **图片上传部分** 是这个结构：

```html
<div class="form-group">
    <label class="control-label col-xs-12 col-sm-2">介绍图片:</label>
    <div class="col-xs-12 col-sm-8">
        <div class="input-group">
            <!--                value="{$row.img|htmlentities}"         这句是回显的关键-->   
            <input id="c-img" class="form-control" name="row[img]" type="text" value="{$row.img|htmlentities}">
            <div class="input-group-addon no-border no-padding">
                <span>
                    <button type="button" id="plupload-img"
                        class="btn btn-danger plupload"
                        data-input-id="c-img"
                        data-preview-id="p-img"
                        data-mimetype="image/gif,image/jpeg,image/png,image/jpg"
                        data-multiple="false">
                        <i class="fa fa-upload"></i> 上传
                    </button>
                </span>
                <span>
                    <button type="button" id="fachoose-img"
                        class="btn btn-primary fachoose"
                        data-input-id="c-img"
                        data-preview-id="p-img"
                        data-mimetype="image/*"
                        data-multiple="false">
                        <i class="fa fa-list"></i> 选择
                    </button>
                </span>
            </div>
        </div>
        <!-- ✅ 这里是预览区域 -->
        <ul class="row list-inline plupload-preview" id="p-img"></ul>
    </div>
</div>
```

### 💡关键点：

- `input` 的 `value="{$row.img|htmlentities}"`
   👉 这行是 **回显的关键**，没有它图片地址不会加载。
- `data-input-id` 和 `data-preview-id` 必须与上面一致。

------

## ✅ 二、确认模型或表字段中确实有值

进入数据库，看看 `hq_company_info` 表中 `img` 字段是否有值，比如：

```
/uploads/20251108/xxxxxx.jpg
```

如果是空的，自然无法回显。

------

## ✅ 三、确认模板中调用了 `Form.api.bindevent`

在 `company_info.js` 的 `edit` 函数里，要确保有这句：

```
Form.api.bindevent($("form[role=form]"));
```

这一行负责自动初始化：

- plupload 上传
- fachoose 文件选择
- 以及预览加载

------

## ✅ 四、如果依然没显示预览，添加这句

有时候因为页面渲染顺序问题，FastAdmin 没来得及初始化预览，可以在 `edit.html` 最后补一段：

```javascript
<script>
    require(['jquery', 'upload'], function ($, Upload) {
        Upload.api.preview($('#c-img').val(), $('#p-img'));
    });
</script>
```

这样页面加载后会手动触发一次图片预览。

------

## ✅ 五、加上多图时的特殊情况（仅单图请忽略）

如果你将 `data-multiple="true"`，那存储的 `img` 会是 JSON 或逗号分隔的多个地址，需要在模型里加个 `getImgAttr()` 格式化：

```javascript
public function getImgAttr($value)
{
    return $value ? cdnurl($value, true) : '';
}
```

------

## ✅ 六、常见错误与解决

| 问题                                              | 原因                            | 解决                                             |
| ------------------------------------------------- | ------------------------------- | ------------------------------------------------ |
| 图片地址存了，但编辑页不显示                      | 没写 `value="{$row.img          | htmlentities}"`                                  |
| 有图片但预览框空白                                | 没有调用 `Form.api.bindevent()` | 补上                                             |
| 上传后预览OK，刷新后丢失                          | 数据库没保存完整路径            | 检查 `upload.php` 返回路径                       |
| 控制台报错 `Upload.api.preview is not a function` | 未加载 `upload` 模块            | 确保页面引入 `require(['jquery','upload'], ...)` |

# 自动写入时间戳设置为datetime格式

> https://www.yuencode.cn/2022/04/14/%E8%A7%A3%E5%86%B3fastadmin%E8%87%AA%E5%8A%A8%E5%86%99%E5%85%A5%E6%97%B6%E9%97%B4%E6%88%B3%E9%97%AE%E9%A2%98/
>
> https://www.luowebs.com/Skill_desc/151/1036.html

**全局配置(不推荐)**

> 不推荐全局改会有问题

```ini
#D:\phpstudy_pro\WWW\work\hq_media\application\database.php
// 自动写入时间戳字段
'auto_timestamp'  => false,
// 时间字段取出后的默认时间格式,默认为Y-m-d H:i:s
'datetime_format' => false,

# 如果需要改为datetime格式需要改为
'auto_timestamp'  => 'datetime',
'datetime_format' => 'Y-m-d H:i:s',
```

**对应数据库**

```sql
CREATE TABLE `hq_project` (
  --忽略具体字段
  `create_time` datetime DEFAULT NULL COMMENT '添加时间',
  `update_time` datetime DEFAULT NULL COMMENT '更新时间',
  `delete_time` datetime DEFAULT NULL,
  PRIMARY KEY (`id`),
  UNIQUE KEY `unique_name` (`name`) USING BTREE
) ENGINE=InnoDB AUTO_INCREMENT=10 DEFAULT CHARSET=utf8mb4 COMMENT='项目';
```



**修改对应模型配置**

```php
<?php

namespace app\admin\model;

use think\Model;


class Project extends Model
{
    // 表名
    protected $name = 'project';

    protected $autoWriteTimestamp = 'datetime'; # 设置 datetime格式
    protected $createTime = 'create_time'; # 改为数据库对应字段
    protected $updateTime = 'update_time';# 改为数据库对应字段
    protected $deleteTime = 'delete_time';# 改为数据库对应字段
    protected $dateFormat = 'Y-m-d H:i:s';  # 改时间格式

    // 追加属性
    protected $append = [

    ];
}

```

#  新增或者编辑后自动刷新首页

这个位置添加 刷新

```shell
api: {
            bindevent: function () {
                Form.api.bindevent($("form[role=form]"), function () {
                    console.log("提交成功");
                    // 提交成功自动刷新父页面
                    // parent.$(".btn-refresh").trigger("click");
                    parent.$("#table").bootstrapTable('refresh');
                });
            }
        }
```

