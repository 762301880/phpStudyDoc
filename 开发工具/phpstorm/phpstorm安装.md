# linux安装phpstorm

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/web_snail/article/details/105695136) |



## 安装

### **安装jdk**

```shell
# 下载jdk
wget https://repo.huaweicloud.com/java/jdk/8u151-b12/jdk-8u151-linux-x64.tar.gz
# 移动并解压
tar -zxvf jdk-8u151-linux-x64.tar -C  /usr/local
# ------------配置环境变量---------------
# 打开文件
vim /etc/profile
# 设置环境变量-以下全部需要添加到/etc/profile文件夹内
 JAVA_HOME=/usr/local/jdk解压的文件夹
 PATH=$JAVA_HOME/bin:$PATH
 CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib.tools.jar
 export JAVA_HOME CLASSPATH PATH
```

**重新加载`/etc/profile`配置文件**

> 环境变量修改后执行source命令，使得配置生效

```shell
source /etc/profile
```

## 安装phpstorm

> 请自行从网络上下载安装包解压到喜欢的目录

```shell
# 例如我的目录
yaoliuyang@yaoliuyang-PC:~/Documents/myapp/PhpStorm-2020.3.3/PhpStorm-203.7717.64/bin$ ls
format.sh  fsnotifier  fsnotifier64  idea.properties  inspect.sh  libdbm64.so  log.xml  ltedit.sh  phpstorm64.vmoptions  phpstorm.png  phpstorm.sh  phpstorm.svg  phpstorm.vmoptions  printenv.py  restart.py


# phpstorm.sh        我们只需要关注这个shell脚本即可
```

**设置别名命令启动**

> **>> /dev/null 2>&1 **      表示启动屏蔽输出
>
> &          仔细看最后面单独设置了一个& 表示后台启动不占用当前终端

```shell
yaoliuyang@yaoliuyang-PC:~$ vim .bashrc         # 用户家目录下编辑别名
# 设置命令
alias phpstorm='sh /home/yaoliuyang/Documents/myapp/PhpStorm-2020.3.3/PhpStorm-203.7717.64/bin/phpstorm.sh >> /dev/null 2>&1 &'

# 刷新添加的别名
source ~/.bashrc
```

**启动phpstorm**

```shell
# 直接终端输入命令phpstorm即可
yaoliuyang@yaoliuyang-PC:~$ phpstorm
```

