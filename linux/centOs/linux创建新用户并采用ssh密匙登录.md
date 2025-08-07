

#  说明&资料

## 说明

> 有时候需要给开发人员一个服务器登录账号用于查看代码调式日志信息等,但是这时候我们不想给他创建密码（**主要是自己懒的记玩意到时候开发人员密码忘了改起来很麻烦，也是问了安全密码有丢失破解的可能**）这时候我们可以采用给创建的用户设置**ssh密匙登录**

## 资料

| 名称        | 地址                                                         |
| ----------- | ------------------------------------------------------------ |
| 第三方博客  | [link](https://blog.csdn.net/weixin_34233421/article/details/92426919?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-5-92426919-blog-125225720.pc_relevant_antiscanv4&spm=1001.2101.3001.4242.4&utm_relevant_index=8)   [link](https://blog.csdn.net/weixin_43860781/article/details/104898684)   [link](https://blog.csdn.net/weixin_44496885/article/details/125225720) |
| openssh官网 | [link](https://www.openssh.com/)                             |

# 操作示例

## **创建用户并添加公钥**

> **注意事项** 
>
> 1. <font color='red'>authorized_keys</font> 文件添加公钥的时候千万别手动复制进来，不然会一只提示公钥没有注册

### **方案一添加公钥(手动上传)**

```shell
# root用户操作

# 添加新用户
[root@VM-16-5-centos ~]# useradd yaoliuyang
# 进入创建的用户家目录
[root@VM-16-5-centos ~]# cd /home/yaoliuyang/
# 创建.ssh目录(如果有的话则不用创建) 并进入
[root@VM-16-5-centos ~]# mkdir .ssh && cd .ssh
# 然后采用rz 命令(见下图)或 ftp 上传本地公钥到服务器
[root@VM-16-5-centos .ssh]# rz
# 将公钥信息写入授权钥匙文件(可以自定义:touch authorized_keys,也可以动态写入:下方命令 ), authorized_keys 
[root@VM-16-5-centos .ssh]# cat id_rsa.pub >> authorized_keys
```

**rz命令将本地文件上传至服务器**

<img src="https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20220627093503429.png" alt="rz-命令上传本地公钥" style="zoom: 80%;" />

###  方案二**ssh-copy-id**命令上传公钥

> 将公钥上传到远程服务器的 `~/.ssh/authorized_keys` 中：
>
> 若远程是 Linux 服务器，可通过 `ssh-copy-id` 上传（需确保服务器开启 SSH）

```shell
ssh-copy-id -i C:\Users\你的用户名\.ssh\id_ed25519.pub 用户名@远程服务器IP
```

1. 若 `ssh-copy-id` 不可用，可手动复制公钥内容，粘贴到远程服务器的 `~/.ssh/authorized_keys` 文件（注意权限：`~/.ssh` 目录权限为 700，`authorized_keys` 为 600）。
2. 测试免密登录：`ssh 用户名@远程服务器IP`，若无需输密码直接登录，则配置成功。



## [**配置 /etc/ssh/sshd_config**](https://man.openbsd.org/sshd_config)

> 以下配置如果有则取消注释或修改,没有则创建即可

```shell
# 自定义添加
RSAAuthentication yes                         #开启RSA验证
PubkeyAuthentication yes                      #是否使用公钥验证
AuthorizedKeysFile   .ssh/authorized_keys     #公钥的保存位置(对应用户的家目录)
#PasswordAuthentication no 禁止使用密码验证登录(这里看个人情况设置)
```

## **重启sshd**生效配置

> 三种重启方法

```shell
systemctl restart sshd

systemctl restart sshd.service

/etc/init.d/sshd restart
```

# 扩展补充

## 通过ssh命令+私钥文件登录服务器

```shell
ssh -i ~/.ssh/id_rsa  用户名@服务器公网地址
```

