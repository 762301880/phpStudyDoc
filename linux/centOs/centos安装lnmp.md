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

# 三、安装 `mysql5.7`

## 3.1 安装`MySQL`

1. 运行以下命令更新YUM源。

   ```shell
   rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
   ```

2. 运行以下命令安装`MySQL`

   **说明** 如果您使用的操作系统内核版本为`el8`，可能会提示报错信息No match for argument。您需要先运行命令**yum module disable mysql**禁用默认的mysql模块，再安装MySQL。

   ```shell
   yum -y install mysql-community-server
   ```

3. 运行以下命令查看MySQL版本号。

   ```shell
   mysql -V
   ```

   返回结果如下所示，表示MySQL安装成功。

   ```shell
   mysql  Ver 14.14 Distrib 5.7.28, for Linux (x86_64) using  EditLine wrapper
   ```

4. 运行以下命令启动MySQL。

   ```shell
   systemctl start mysqld
   ```

5. 运行以下命令设置开机启动MySQL。

   ```shell
   systemctl enable mysqld
   systemctl daemon-reload
   ```

