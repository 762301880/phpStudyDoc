# [修改系统默认源](https://developer.aliyun.com/mirror/debian?spm=a2c6h.13651102.0.0.3e221b113ZXXth)

> 由于deepin使用的是**debian**内核所以我们需要先[查阅debian的版本](https://kzpu.com/archives/3071.html)

**资料**

| 名称                    | 地址                                                         |
| ----------------------- | ------------------------------------------------------------ |
| 阿里云-debian修改镜像源 | [link](https://developer.aliyun.com/mirror/debian?spm=a2c6h.13651102.0.0.3e221b113ZXXth) |

## 修改镜像源

**查询当前正在使用的镜像源**

```shell
cat /etc/debian_version 
10.10
```

**修改镜像源**

> 编辑**/etc/apt/sources.list**文件(需要使用sudo), 在文件最前面添加以下条目(操作前请做好相应备份)
>
> 这里只提供一个版本更多源配置请查阅阿里云

```shell
#  debian 10.x
deb http://mirrors.aliyun.com/debian/ buster main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster main non-free contrib
deb http://mirrors.aliyun.com/debian-security buster/updates main
deb-src http://mirrors.aliyun.com/debian-security buster/updates main
deb http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster-updates main non-free contrib
deb http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
deb-src http://mirrors.aliyun.com/debian/ buster-backports main non-free contrib
```