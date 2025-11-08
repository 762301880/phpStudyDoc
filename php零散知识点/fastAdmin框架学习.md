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