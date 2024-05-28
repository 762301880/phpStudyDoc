#  搭建文件管理系统

**先上效果**

![image-20231108155732385](https://gitee.com/yao_liuyang/blogImages/raw/master/blogImages/image-20231108155732385.png)

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

#  nginx 配置websocket转发连接

**资料**

| 名称                    | 地址                                                      |
| ----------------------- | --------------------------------------------------------- |
| 官方文档 WebSocket-代理 | [link](http://nginx.p2hp.com/en/docs/http/websocket.html) |

**示例配置**

> - `listen 80;`：指定Nginx监听的端口，这里使用的是80端口，你可以根据自己的需求进行更改。
> - `server_name your_domain.com;`：替换为你的域名，这样Nginx将会与该域名关联。
> - `location /`：这是Nginx配置中用于匹配URL路径的部分。
> - `proxy_http_version 1.1;`、`proxy_set_header Upgrade $http_upgrade;` 和 `proxy_set_header Connection "upgrade";`：这几行配置用于启用WebSocket代理所必需的HTTP版本和消息头。
> - `proxy_set_header X-Real-IP $remote_addr;` 和 `proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;`：这两行配置用于传递客户端的真实IP地址给Swoole WebSocket服务器。
> - `proxy_set_header Host $host;`：这行配置用于传递客户端请求的源主机头给Swoole WebSocket服务器。
> - `proxy_pass http://swoole_websocket_container:9501;` ：替换为你的Swoole WebSocket容器的名称和端口，这将告诉Nginx将请求发送到与该容器关联的地址和端口。
>
> 通过修改这个示例配置中的注释部分，你可以将其适配到你自己的Nginx和Swoole WebSocket环境中，以实现WebSocket的代理连接。

```nginx
server {
    listen 80; # 监听端口，根据需要进行更改
    server_name your_domain.com; #  # 替换为你的域名或ip地址
    # 如果包含ws请求则会转发到此 
    location /ws {
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $host;

        proxy_pass http://swoole_websocket_container:9501; // 这里的swoole_websocket_container是你的Swoole WebSocket容器的名称
        # 例如我dockercompose.yml services 名称是 app 则上面的对应示例因该改为
        # proxy_pass http://app:9501;   
    }
}
```

**示例连接websocket**

> 直接连接nginx容器/ws就可以连接了
>
> 1997是指docker容器的端口号

```php
ws://60.204.148.255:1997/ws
```

