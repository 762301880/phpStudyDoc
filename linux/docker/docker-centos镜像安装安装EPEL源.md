# 说明

> 由于docker的镜像都是阉割版，内置的yum命令不多
>
> [**百度介绍**](https://jingyan.baidu.com/article/cdddd41c9ab07553cb00e1a7.html)
>
> epel源比官方的源软件更丰富，比源码编译安装简单方便，适合于redhat、centos系统。

# 资料

| 名称           | 地址                                           |
| -------------- | ---------------------------------------------- |
| epel-rpm包地址 | [link](https://dl.fedoraproject.org/pub/epel/) |



# 安装

**yum安装**

```shell
yum -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

**rpm安装**

```shell
# 下载rpm包
wget https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
# rpm安装
rpm -ivh https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm
```

## 实验

> 安装完成之后我们可以跑一个**不常见的yum命令**,
>
> 输入***sl***及可以看见好玩的跑火车命令了

```php
# 安装sl
yum -y install sl 
```

