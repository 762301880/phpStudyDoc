# linux安装phpstorm

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/web_snail/article/details/105695136) [link](https://blog.csdn.net/weixin_30609287/article/details/99657620?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-99657620-blog-105695136.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-99657620-blog-105695136.pc_relevant_aa&utm_relevant_index=1) |



## 安装

### **安装jdk**

```shell
# 下载jdk
wget https://repo.huaweicloud.com/java/jdk/8u151-b12/jdk-8u151-linux-x64.tar.gz
# 移动并解压
tar -zxvf jdk-8u151-linux-x64.tar.gz -C /usr/local/
# ------------配置环境变量---------------
# 打开文件
vim /etc/profile
# 设置环境变量-以下全部需要添加到/etc/profile文件夹内
 JAVA_HOME=/usr/local/jdk解压的文件夹       # 例子 /usr/local/jdk1.8.0_151
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

### **设置别名命令启动**

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
[1] 21823
```

### **创建桌面图标启动**

**参考资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/wfk2975019671/article/details/107641756?spm=1001.2101.3001.6661.1&utm_medium=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-107641756-blog-82495099.pc_relevant_multi_platform_whitelistv1_exp2&depth_1-utm_source=distribute.pc_relevant_t0.none-task-blog-2%7Edefault%7ECTRLIST%7Edefault-1-107641756-blog-82495099.pc_relevant_multi_platform_whitelistv1_exp2&utm_relevant_index=1) |

**示例**

> **在桌面创建** ***phpstorm.desktop***文件 然后编辑
>
> ## 参数说明
>
> **Exec**执行的shell脚本地址
>
> **Icon** 指向图标地址

```shell
# phpstorm.desktop
[Desktop Entry]
Type=Application
Version=2020.3
GenericName=Phpstorm2020
Name=phpstorm
Comment=phpstorm ide
Exec="/home/yaoliuyang/Documents/myapp/PhpStorm-2020.3.3/PhpStorm-203.7717.64/bin/phpstorm.sh"  
Icon=/home/yaoliuyang/Documents/myapp/PhpStorm-2020.3.3/PhpStorm-203.7717.64/bin/phpstorm.svg
Terminal=false
Categories=Development;IDE;
StartupNotify=true
```

