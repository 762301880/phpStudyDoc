# 使用yum命令安装

## 参考文档

| 参考地址       | 链接                                                         |
| -------------- | ------------------------------------------------------------ |
| 阿里云帮助文档 | [点我跳转](https://help.aliyun.com/document_detail/171940.html?spm=a2c4g.11186623.6.1259.1c455c1fjdgmNv) |



## 使用yum 命令安装

```shell
	yum search php # 搜索php
	yum -y install php # 安装php 默认安装的是5.4的版本
	yum list php* # 列出所有版本的php
```



- 安装指定版本的php

``` shell
yum -y install php
```

## 查看php 的版本

```shell
php --version 
```



# 卸载php

- 使用`yum`命令卸载

```shell
yum -y remove php # 此命令无法全部卸载php
```

- rpm 命令查询

```shell
rpm -qa|grep php
```

- 使用`rpm -e + rpm包名卸载php`

```shell
rpm -e ......
```

