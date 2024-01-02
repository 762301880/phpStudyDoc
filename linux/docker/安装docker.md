## centos-linux安装

```shell
# 更新系统软件包：
sudo yum update -y

# 安装依赖软件包：
sudo yum install -y yum-utils device-mapper-persistent-data lvm2

# 添加Docker仓库：
sudo yum-config-manager --add-repo https://download.docker.com/linux/centos/docker-ce.repo

# 安装Docker CE：
sudo yum install -y docker-ce

# 启动Docker服务：
sudo systemctl start docker

# 设置Docker服务开机自启：
sudo systemctl enable docker
```

### bug解析

**报错**

```shell
Transaction check error:
  file /usr/bin/docker from install of docker-ce-cli-1:24.0.7-1.el7.x86_64 conflicts with file from package docker-common-2:1.13.1-209.git7d71120.el7.centos.x86_64
  file /usr/bin/dockerd from install of docker-ce-3:24.0.7-1.el7.x86_64 conflicts with file from package docker-common-2:1.13.1-209.git7d71120.el7.centos.x86_64

Error Summary
------------
```

> 这个错误表明在安装 Docker 时，存在文件冲突。你可以尝试以下方法解决这个问题：

```shell
# 首先，卸载已经安装的 Docker 相关的软件包
sudo yum remove docker-ce-cli docker-ce-3 docker-ce-2 docker-common
# 然后，清除 yum 缓存并更新软件包列表
sudo yum clean all
sudo yum makecache fast
```

## windows安装docker

**资料**

| name                        | url                                                          |
| --------------------------- | ------------------------------------------------------------ |
| wsl安装官方文档             | [link](https://learn.microsoft.com/zh-cn/windows/wsl/install-manual) |
| 菜鸟教程-win-docker安装教程 | [link](https://www.runoob.com/docker/windows-docker-install.html) |













