# 一、使用apt命令安装

- 官方ubntu安装docker[文档](https://docs.docker.com/engine/install/ubuntu/)

```shell
sudo apt install docker.io
```

## 安装完成之后启动docker

```shell
sudo systemctl start docker 
```

## 删除docker

```shell
sudo apt-get remove --auto-remove docker

# 使用dpkg查询已安装包，针对性删除
# 查询相关软件包
dpkg -l | grep docker
# 删除这个包
sudo apt remove --purge dock.io
```

