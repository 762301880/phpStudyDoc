# 一 、Ubuntu安装postman

## 1.1 使用`wget`命令从网络下载

```shell
wget https://dl.pstmn.io/download/latest/linux64
```

## 1.2解压下载的postman

```shell
tar -zxvf  +需要解压的postman包
```

## 1.3打开postman

```shell
cd Postman/ #进入解压的postman目录
./Postman   #打开postman软件
```

# 二、Ubuntu安装typota

linux安装typora[官网文档](https://www.typora.io/#linux)

```shell
# or run:
# sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys BA300B7755AFCFAE
wget -qO - https://typora.io/linux/public-key.asc | sudo apt-key add -
# add Typora's repository
sudo add-apt-repository 'deb https://typora.io/linux ./'
sudo apt-get update
# install typora
sudo apt-get install typora
```

# 三、ubuntu安装mysql客户端 dbeaver

- 官网[下载地址](https://dbeaver.io/download/)

## 3.1 使用命令下载deb包

```shell
wget https://dbeaver.io/files/dbeaver-ce_latest_amd64.deb
```

- 安装同上
