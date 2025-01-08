#  资料

| 名称       | 地址                                           |
| ---------- | ---------------------------------------------- |
| 第三方博客 | [link](https://zhuanlan.zhihu.com/p/362315737) |



## nginx基本概念

```shell
# nginx基本概念
1 nginx 是什么可以做什么事情
2 反向代理
3 负载均衡
4 动静分离
```

> nginx配置文件 /etc/nginx/nginx.conf
>
> **配置文件的几大快**
>
> nginx 配置文件主要分为六个区域:核心区域 main(全局设置) 作用域是全局
>
> events(nginx工作模式)
>
> http(http设置)
>
> upstream(负载均衡服务器配置)
>
> server(主机配置)
>
> location(URL匹配)

```shell

yaoliuyang@yaoliuyang-PC:/etc/nginx$ cat nginx.conf 
# 全局块 从配置文件到events块的内容，主要会设置一些影响nginx服务器整体运行的配置指令，主要包括配置运行Nginx服务器的用户（组），允许生成worker processes数，进程PID存放路径，日志存放路径和类型以及配置文件引入等。

user www-data;
# 这是nginx处理并发的关键配置，worker_processes 值越大，可以支持的并发数量也就越多，但是会受到硬件，软件等设备的制约
worker_processes auto;
pid /run/nginx.pid; #进程id
include /etc/nginx/modules-enabled/*.conf;
# events块 涉及的指令主要影响nginx服务器与用户的网络连接，常用的设置包括是否开启对多worker process 下的网络连接进行序列化，是否允许同时接受多个网络连接，选取那种事件驱动模型来处理连接请求，每个word process 可以同时支持最大连接数等
events {
        worker_connections 768;
        # multi_accept on;
}
# http块 http全局块配置包括文件引入，MIME-TYPE定义，日志自定义（access_log，error_log）,连接超时时间，单链接请求数上线等 包括http全局块，server块（include 包含的文件夹中写包含的server块）
http {
        ##
        # Basic Settings
        ##
        sendfile on; # 开启文件上传下载
        tcp_nopush on;
        tcp_nodelay on;
        keepalive_timeout 65; # nginx超时时间
        types_hash_max_size 2048;
        # server_tokens off;
        # server_names_hash_bucket_size 64;
        # server_name_in_redirect off;
        # 来着定文件的mime类型，类型在配置文件目录下的mime.type文件定义,来告诉nginx来识别文件类型
        include /etc/nginx/mime.types; 
        # 设定了默认的类型为二进制流,也就是当文件类型未定义时使用这种下载方式,例如在没有配置asp的locate环境时，nginx是不予解析的，此时，用浏览器
        # 访问asp文件就会出现下载了
        default_type application/octet-stream;
        ##
        # SSL Settings
        ##
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2; # Dropping SSLv3, ref: POODLE
        ssl_prefer_server_ciphers on;
        ##
        # Logging Settings 日志记录位置
        ##
        access_log /var/log/nginx/access.log;
        error_log /var/log/nginx/error.log;
        ##
        # Gzip Settings
        ##
        gzip on;
        # gzip_vary on;
        # gzip_proxied any;
        # gzip_comp_level 6;
        # gzip_buffers 16 8k;
        # gzip_http_version 1.1;
        # gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

        ##
        # Virtual Host Configs
        ##

        include /etc/nginx/conf.d/*.conf;   #  例如 127.0.0.1.conf 配置文件
        include /etc/nginx/sites-enabled/*; # sites-enabled 意为已开启的网站
}


#mail {
#       # See sample authentication script at:
#       # http://wiki.nginx.org/ImapAuthenticateWithApachePhpScript
# 
#       # auth_http localhost/auth.php;
#       # pop3_capabilities "TOP" "USER";
#       # imap_capabilities "IMAP4rev1" "UIDPLUS";
# 
#       server {
#               listen     localhost:110;
#               protocol   pop3;
#               proxy      on;
#       }
# 
#       server {
#               listen     localhost:143;
#               protocol   imap;
#               proxy      on;
#       }
#}
```

**server块**

> 

```nginx
server {
        listen 80 default_server; #监听的端口号80端口
        server_name www.cs.com; # 主机名

        root /var/www/html;

        # Add index.php to the list if you are using PHP
        index index.html index.htm index.nginx-debian.html;

        server_name _; #服务名称
        # /代表所有的请求都会进入到这里面
        location / {
                # First attempt to serve request as file, then
                # as directory, then fall back to displaying a 404.
                try_files $uri $uri/ =404;
        }
}
```

# nginx配置实例

> location指令说明
>
> 1. = ：用于不含正则表达式的 uri 前，要求请求字符串与 uri 严格匹配，如果匹配成功，就停止继续向下搜索并立即处理该请求。
> 2. ~：用于表示 uri 包含正则表达式，并且区分大小写。 
> 3. ~：用于表示 uri 包含正则表达式，并且不区分大小写。 
> 4. ^~：用于不含正则表达式的 uri 前，要求 Nginx 服务器找到标识 uri 和请求字符串匹配度最高的 location 后，立即使用此 location 处理请求，而不再使用 location 块中的正则 uri 和请求字符串做匹配。 注意：如果 uri 包含正则表达式，则必须要有 ~ 或者 ~ 标识。

## 反向代理

> 实现效果打开浏览器，在地址栏中输入地址：www.123.com,跳转 www.baidu.com
>
> www.123.com 可以在/etc/hosts中添加一个虚拟域名

```nginx
#  监听本地80端口 服务名称是www.123.com  路径是 /的时候转发到 www.baidu.com
server{
   listen 80;
   server_name www.123.com;
   location / {
      root html;
      proxy_pass http://www.baidu.com;
      index index.html index.htm;
   }
}
# 重启nginx nginx -s reload
```

- 反向代理实例2

> 实现效果：使用nginx反向代理，根据访问的路径跳转到不同端口的服务中，
>
> nginx监听端口为1997
>
> 访问 www.123.com:1997/bd 直接跳转到www.baidu.com
>
> 访问www.123.com:1997/al 直接跳转到www.aliyun.com

```nginx
server{
   listen 1997;
   server_name www.123.com;
   # ~类似于正则表达式通配符 
   location ~ /bd/ {
      proxy_pass http://www.baidu.com;
   }
  location ~ /al/ {
     proxy_pass http://www.aliyun.com;   
  }  
}
```

## 负载均衡

> **概念**
>
> 增加服务器的数量，然后将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况
>
> 改为将请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡
>
> **实现效果**
>
> 浏览器的地址栏输入地址 www.yaoliuyang.com 负载均衡的效果









# nginx 小白学习

学习Nginx对于初学者来说确实可能会有些挑战，但通过有计划的学习步骤，你可以逐步掌握它。以下是学习Nginx的一些建议：

- **了解基础概念**
  - 学习什么是Nginx，它的主要功能（如反向代理、负载均衡等）。
  - 理解Web服务器的基本工作原理。

- **安装与配置**
  - 在本地环境中安装Nginx，可以是Linux、Windows或macOS。
  - 学习如何启动、停止和重启Nginx服务。
  - 掌握基本的配置文件语法，包括`server`块、`location`块等。

- **实践操作**
  - 尝试配置一个简单的静态网站。
  - 实现URL重写规则。
  - 配置SSL/TLS以支持HTTPS。

- **深入理解**
  - 学习Nginx作为反向代理服务器的配置方法。
  - 探索如何使用Nginx进行负载均衡。
  - 了解性能优化技巧，如缓存设置等。

- **参考资源**
  - 官方文档：https://nginx.org/en/docs/
  - 在线教程和视频课程。
  - 参与社区讨论，如Stack Overflow上的Nginx标签。

- **持续练习**
  - 不断尝试新的配置选项。
  - 解决遇到的问题，积累经验。

记住，最好的学习方式就是动手实践。从简单的配置开始，随着你对Nginx的理解加深，逐渐尝试更复杂的设置。





学习Nginx对于初学者来说确实可能会有些挑战，但通过有计划的学习步骤，你可以逐步掌握它。以下是学习Nginx的一些建议：

- **了解基础概念**
  - 学习什么是Nginx，它的主要功能（如反向代理、负载均衡等）。
  - 理解Web服务器的基本工作原理。

- **安装与配置**
  - 在本地环境中安装Nginx，可以是Linux、Windows或macOS。
  - 学习如何启动、停止和重启Nginx服务。
  - 掌握基本的配置文件语法，包括`server`块、`location`块等。

- **实践操作**
  - 尝试配置一个简单的静态网站。
  - 实现URL重写规则。
  - 配置SSL/TLS以支持HTTPS。

- **深入理解**
  - 学习Nginx作为反向代理服务器的配置方法。
  - 探索如何使用Nginx进行负载均衡。
  - 了解性能优化技巧，如缓存设置等。

- **参考资源**
  - 官方文档：https://nginx.org/en/docs/
  - 在线教程和视频课程。
  - 参与社区讨论，如Stack Overflow上的Nginx标签。

- **持续练习**
  - 不断尝试新的配置选项。
  - 解决遇到的问题，积累经验。

记住，最好的学习方式就是动手实践。从简单的配置开始，随着你对Nginx的理解加深，逐渐尝试更复杂的设置。