# 说明



## 参考资料

| 名称       | 地址                                                      |
| ---------- | --------------------------------------------------------- |
| 第三方博客 | [link](https://www.jb51.net/article/159383.htm)           |
| linux公社  | [link](https://www.linuxidc.com/Linux/2020-02/162379.htm) |

# 安装

> 添加PHP PPA存储库
>
> 首先，添加ppa:ondrej/php PPA存储库，它具有最新的PHP构建包。 键入以下命令以添加存储库。 在提示时按Enter键添加存储库。

```shell
 sudo apt-get install software-properties-common
 sudo add-apt-repository ppa:ondrej/php # 走到这一步的时候会卡住按enter键继续即可
 sudo apt-get update g
```



```shell
sudo -y apt install php7.4 
sudo -y apt install php7.4-fpm
```

