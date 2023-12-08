#  搭建文件管理系统

**先上效果**

![image-20231108155732385](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/image-20231108155732385.png)

配置nginx

> **在 /etc/nginx/conf.d**   下创建  **对应域名.conf** 配置  创建**default.conf**
>
> <font color='red'>注意 /root   不要挂载到这个文件夹下</font>

```shell
server {
	    listen 8089; # 设置端口 
	    server_name localhost; # 对应域名或公网ip地址_默认域名是localhost
		location /files {  # 匹配后缀 如果只是 / 就是默认打开根域名直接跳转过来
		alias /data/;  # 指定映射资源目录
		autoindex on; 
     }
}

# 这是一个Nginx服务器的配置，用于监听8089端口，并设置服务器名称为60.204.148.255。在/files路径下，使用alias指令将请求映射到/data/目录，并启用自动索引功能。
```

**最后记得重启 nginx**

```nginx
systemctl restart nginx
```

