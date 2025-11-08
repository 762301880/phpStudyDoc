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

