# 一、资料

## 1.1阿里云`nginx`搭建`lnmp`[文档](https://help.aliyun.com/document_detail/97251.html)

```shell
https://help.aliyun.com/document_detail/97251.html
```

# 二、安装`nginx`

## 2.1 使用`yum`命令安装

```shell
yum install -y nginx
```

- 查看是否安装成功

```shell
[root@VM-24-20-centos conf.d]# nginx -v
nginx version: nginx/1.20.1
```

## 2.2 配置nginx

### 2.1运行以下命令查看`Nginx`配置文件的默认路径。

```shell
cat /etc/nginx/nginx.conf
```

### 2.2在`http`大括号内，查看`include`配置项。即配置文件的默认路径。

![conf](https://yaoliuyang-blog-images.oss-cn-beijing.aliyuncs.com/blogImages/p130116.png)

### 2.3在配置文件的默认路径下，备份默认配置文件。

```shell
cd /etc/nginx/conf.d
cp default.conf default.conf.bak
```

