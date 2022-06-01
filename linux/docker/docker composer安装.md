## [docker-composer安装](https://www.runoob.com/docker/docker-compose.html)

**资料**

| 名称                  | 地址                                                         |
| --------------------- | ------------------------------------------------------------ |
| docker官网compose安装 | [link ](https://docs.docker.com/compose/install/)  [国内镜像安装](https://get.daocloud.io/) |
| 第三方博客            | [link](https://yeasy.gitbook.io/docker_practice/compose/install) |

**linux安装**

```shell
# 运行以下命令以下载 Docker Compose 的当前稳定版本

sudo curl -L "https://github.com/docker/compose/releases/download/v2.2.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

# 创建可执行权限

sudo chmod +x /usr/local/bin/docker-compose

# 创建软链

sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose

# 测试是否安装成功：

docker-compose --version


# 以上下载很慢可以用国内的镜像(你可以通过修改URL中的版本，可以自定义您的需要的版本。)

curl -L https://get.daocloud.io/docker/compose/releases/download/v2.2.3/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose
chmod +x /usr/local/bin/docker-compose
```

