	[^author:姚留洋]

[^created_at:2021/10/24]

## ln软连接 

菜鸟教程[Ln](https://m.runoob.com/linux/linux-comm-ln.html)

> inux ln（英文全拼：link files）命令是一个非常重要命令，它的功能是为某一个文件在另外一个位置建立一个同步的链接。
>
> 当我们需要在不同的目录，用到相同的文件时，我们不需要在每一个需要的目录下都放一个必须相同的文件，我们只要在某个固定的目录，放上该文件，然后在 其它的目录下用ln命令链接（link）它就可以，不必重复的占用磁盘空间。
>
> 个人理解
>
> 总体来说就是我们经常需要cd什么什么目录去找一个常需要打开的目录，
>
> 我们可以将此目录挂载到~目录中方便下次使用

**建立软连接示例**

```shell
# 软连接
ln -s 源目录(可以不写,如果不写直接将链接目录同步过来)  
ln -s 需要链接的目录 原始文件路径  快件方式的路径
# 示例
ln -s /test  #将根目录下的test目录挂载到~目录(注意我的这个示例命令是在~目录中执行的) 
ln -s /word/laravel_study  ~/word/laravel_study  # 将根目录下面的文件项目软连接到用户家目录
# 查看文件软连接的路径
ll 文件名称
其中,文件类型位置的'l'表示其类型为link(连接类型),后面的'->'指向的是文件原始文件
# 删除挂载的目录
ln -rm ~/test  # 删除~目录中链接的test目录,/目录下的test目录不会消失 
```



**软链接**：

- 1.软链接，以路径的形式存在。类似于Windows操作系统中的快捷方式
- 2.软链接可以 跨文件系统 ，硬链接不可以
- 3.软链接可以对一个不存在的文件名进行链接
- 4.软链接可以对目录进行链接

**硬链接**：

- 1.硬链接，以文件副本的形式存在。但不占用实际空间。
- 2.不允许给目录创建硬链接
- 3.硬链接只有在同一个文件系统中才能创建

**必要参数**：

- -b 删除，覆盖以前建立的链接
- -d 允许超级用户制作目录的硬链接
- -f 强制执行
- -i 交互模式，文件存在则提示用户是否覆盖
- -n 把符号链接视为一般目录
- -s 软链接(符号链接)
- -v 显示详细的处理过程

**选择参数**：

- -S "-S<字尾备份字符串> "或 "--suffix=<字尾备份字符串>"
- -V "-V<备份方式>"或"--version-control=<备份方式>"
- --help 显示帮助信息
- --version 显示版本信息





## 输出重定向

> 一般命令的输出都会显示在终端中,有时候需要将一些命令的执行结果保存到文件中
>
> 进行后寻的分析/统计，则这时候需要使用到的输出重定向技术。

**命令**

```shell
> # 覆盖输输出（一个大于号），会覆盖原有的文件内容
>> # 追加输出（两个个大于号），不会不该原始文件内容，会在原始内容末尾继续添加
# 语法 正常执行的指令 >/>> 文件的路径
# 注意 ：文件可以不存在,不存在则新建
# 案例：使用覆盖重定向,保存ls -la 的执行结果,保存到当前目录下的ls.txt
```

**案例**

```shell
# 覆盖输出 >
yaoliuyang@benben:~$ touch demo.txt 
yaoliuyang@benben:~$ ls
公共的  模板  视频  图片  文档  下载  音乐  桌面  demo.txt  sensors  snap
yaoliuyang@benben:~$ echo 测试覆盖文字1 > demo.txt 
yaoliuyang@benben:~$ cat demo.txt 
测试覆盖文字1
yaoliuyang@benben:~$ echo 测试覆盖文字2 > demo.txt 
yaoliuyang@benben:~$ cat demo.txt 
测试覆盖文字2
# 追加输出 >>
yaoliuyang@benben:~$ echo 追加文字 >> demo.txt 
yaoliuyang@benben:~$ cat demo.txt 
测试覆盖文字2
追加文字

# 输出变量到文件    https://www.cnblogs.com/moonandstar08/p/7282203.html
# 输出当前的时间到text文件中
echo $(date "+%Y-%m-%d %X") >> text.txt
```

## 管道命令 

> 管道符 `|`,就是俗话所说的竖线
>
> **作用**
>
> 管道一般可以用来**过滤**,**特殊**,**扩展处理**
>
> **语法**
>
> 管道不可以单独使用必须配合一些命令使用,辅助作用

**案例**

**过滤例子**

```shell
# 筛选/目录下名称为o的目名称
yaoliuyang@yaoliuyang-PC:/$ ls | grep o
boot
home
lost+found
opt
proc
recovery
root
```

## 操作服务器的主机名

> `hostname`   读取设置主机名

```shell
# 查看当前的主机名 含义:表示输出完整的主机名 
$ hostname 
yaoliuyang-PC
# 含义:表示输出当前主机名中的FQDN(全限定域名)
$ hostname -f
yaoliuyang-PC
```

### 修改主机名称

```shell
#修改主机名，其中new_hostname是你想要设置的新主机名：(例子  hostnamectl set-hostname yaoliuyang )  hostnamectl set-hostname 是固定死的参数
hostnamectl set-hostname new_hostname
#重启系统以使更改生效。你可以使用以下命令来重启系统
reboot
```



## 处理文件命令:sed

[sed命令详解](https://www.runoob.com/linux/linux-comm-sed.html)

### 替换文件中的字符

> 假如现在有一个文件a.txt，如下图所示我想将 www.baidu.com 修改为 www.aliyun.com

```shell
[root@VM-16-5-centos ~]# cat a.txt 
"www.baidu.com@1"
"www.baidu.com@2"
"www.baidu.com@3"
"www.baidu.com@4"
"www.baidu.com@5"
# 命令如下 -i insert 可以理解为插入的意思   s select 可以理解为选择的意思  g global 全局的意思
[root@VM-16-5-centos ~]# sed -i  's/www.baidu.com/www.aliyun.com/g' a.txt 
[root@VM-16-5-centos ~]# cat a.txt 
"www.aliyun.com@1"
"www.aliyun.com@2"
"www.aliyun.com@3"
"www.aliyun.com@4"
"www.aliyun.com@5"

```



## [显示当前进程信息](https://www.runoob.com/linux/linux-comm-ps.html)

> 指令：**ps**
>
> 作用：主要查看服务器的进程信息
>
> 选项含义
>
> ​    -e:  等价于**-A** 表示列出全部的进程
>
> ​    -f:  显示全部的列

**示例**

```shell
ps -ef | grep 进程关键字

# ps -ef  

UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 11:43 ?        00:00:05 /sbin/init splash
root         2     0  0 11:43 ?        00:00:00 [kthreadd]

# UID 该进程执行的用户id
# PID 进程id
# PPID 该进程的父级进程id,如果一个程序的父级进程找不到,该程序的进程称之为僵尸进程(没什么用了)
# C  Cpu的占用率,其形式是百分数
# STIME  Start_time,该进程的启动事件
# TTY 终端设备,发起该进程的设备识别符号,如果显示“?”意思是不是由终端发起的进程,
# TIME 进程的执行时间
# CMD  进程对应的名称或者对应的路径
```

## 上传文件到服务器(rz)或下载文件到本地(sz)

**参考资料**

| 名称           | 地址                     |
| -------------- | ------------------------ |
| 第三方博客参考 | [link](本地文件到服务器) |

```shell
# 语法命令
sz  [文件夹或文件地址路径]                          # 将服务器上的文件拷贝至本地

rz [文件夹或文件地址路径]                           # 将本地文件或文件夹上传至服务器
```

## vim命令

**资料**

| 名称         | 地址                                                |
| ------------ | --------------------------------------------------- |
| 菜鸟教程-vim | [link](https://www.runoob.com/linux/linux-vim.html) |
| vim-中文官网 | [link](http://vimcdoc.sourceforge.net/)             |

### **常用命令示例**

```shell
# 光标操作
gg  #跳转到首行(小写)
G   # 跳转到末行(shift +g  或者 CapsLock +g)
shift + ^ # shift + 最上排的数字6 跳转到一行的开始(正则表达式开始)
shift + $ # shift + 最上排的数字4 跳转到一行的末尾(正则表达式结束)
ctrl+b(before)  or pageup# 向上 翻屏
ctrl+f()  or pagedown# 向下 翻屏

# 复制操作
# 1 复制光标所在行
yy           # 按键复制
p            # 按键黏贴所复制的行
# 2 以光标所在行为准,向下复制指定的行数
数字+yy  # 数字+yy 例  3yy   以光标所在行向下复制三行(包含当前光标行再向下两行) 

# 3.可视化复制(用的比较少)
按键：ctrl+v,然后按下↑↓←→方向键选中需要复制的区块,按下yy键进行复制,最后按下p键黏贴

# 剪切/删除
# 1. 剪切删除光标所在行
dd  # 删除之后下一行上移
dd 之后 再 p # 先剪贴再黏贴==剪切 (如果剪切了不粘帖就是删除效果)
n + dd  # n 为数字。剪切光标所在的向下 n 行，例如 20dd 则是剪切 20 行(常用),用p可以粘贴。
# 2. 剪切/删除光标所在行,但是删除之后下一行不上移
D # 大写的D  shift d


# 撤销/恢复(win ctrl+z 撤销 ctrl+y恢复)
# 1.撤销命令
:u  # 命令行输入字母u  Enter键确定(重复撤销 重复操作):undo 撤销 
u # 命令模式 直接u  重复撤销重复操作
# 2. 恢复
ctrl + r # 恢复(取消)之前的撤销:restore 恢复

#********************************************************************#
# 格式化文件(参考 https://wenku.baidu.com/view/206645f687254b35eefdc8d376eeaeaad1f316de.html)
<<!
在vim的命令模式下，输⼊=，vim进⼊格式化状态，在编辑屏幕的右下⾓可以看到有=，表⽰已经进⼊了格式化模式，此时输⼊的任何命令
都是格式化操作，⾮法命令则不解释。
!
# 1. 常见操作：
格式化光标⾏开始的3⾏
在格式化模式下输⼊3=
=3=
格式化当前⾏到第25⾏的代码：
在格式化模式下输⼊25G
=25G
格式化{}内的代码段：
将光标移到{}的上（{或者}均可），在格式化模式下输⼊%
=%
格式化#if #else #endif之间的代码：
将光标移到#if #else上，在格式化模式下输⼊%：
=%
将会格式化其中⼀个选择项的代码。
如果将光标放在#endif上，进⼊格式化模式，再输⼊%，则会格式化整个条件匹配的代码。
=%
格式化整篇⽂档全部：
在格式化模式下输⼊G
=G
或者直接在命令模式下输⼊
gg=G
#********************************************************************#

# 光标的快速移动 
n+G          # 快速将光标移动到指定的位置(数字+大写的G) G可以理解为：Go to Line:Column  跳转到第几行
n+↑/↓        # 以当前光标为准向上/向下/向下移动n行(数字+上键/下键)
n+←/→        # 以当前光标为准像左/右移动移动n字符(数字+左/右)
: n          # 末行模式下(: 冒号输入命令的模式下) 冒号输入字母上排的数字  Enter键确定

# 模式间的切换
# 1. 末行模式
进入方式:由命令模式进入,按下":"即可进入末行方式
:wq  #冒号代表的是末行模式因为在最后一行(退出 1.按一下esc(会等待一会) 2.按两下esc 立即退出 3. 删除末行下的命令包括冒号)
# 2.保存操作(write)
输入: ":w"  保存文件
输入: ":w 路径" 另存为
# 3.退出(quit)
输入: ":q"  退出文件
输入: ":wq"  保存并退出
输入: "q!" 强制退出不保存
# 4.调用外部命令
:!ls # 例如我们在文件中想调用外部命令(vim里面调用外部命令) !+命令
# 6.搜索(末行模式下输入反斜杠/+搜索的关键字 Enter键盘第一次确认,再次向下搜索使用按键n)
:/关键词  #例如 
:nohl  #[no highlight] 取消高亮,上一步查询之后搜索的关键词依然会保持高亮状态,如果觉得不舒服可以取消

# 7.替换指令 
:s/搜索的关键词/新的内容 # [strreplace] 如果在光标所在行进入命令模式下替换命令,则会匹配光标所在行的关键词进行替换 (替换光标所在行第一处符合条件的内容)
:s/搜索的关键词/新的内容/g # 替换光标所在行的全部符合条件的内容
:%s/搜索的关键词/新的内容  # 替换全局所有行第一处符合条件的内容
:%s/搜索的关键词/新的内容/g  # 替换全局所有行所有符合条件的内容  %表示整个文件,g表示全局(global)

# 8.显示行号
:set nu  # 显示行号
:set number # 显示行号全拼
:set nonu # 取消显示行号

# 9. 退出方式
回顾:之前vim中退出编辑的文件可以使用":q"或者":wq"
除了上面的这个语法之外,vim还支持另外一个保存退出方式":x"
:x    # 在文件没有修改的情况下,表示直接退出,在文件修改的情况下表示保存并退出,如果文件没有被修改(这时候也习惯退出用wq),则文件的修改时间会被更新,但是如果文件没有被修改,使用x进行退出的话,则文件修改时间不会被更新的,主要是会混淆用户对文件的修改时间的认定,

# 10,文件加密&解密
:X  # 加密 输入两次密码(wq确认)
:X  # 解密 输入两次空(按两下回车键确认)
```

### **使用vim打开多个文件,在末行模式下进行切换文件 **

```php
# 打开多个文件命令
vim 文件路径1 文件路径2 文件路径3 作用:同时打开多个文件 # 会默认打开文件1  例 vim demo01.txt demo.txt
# 查看当前已经打开的文件名称: ":files"
:files 
# 例    
:files
  1 %a   "demo01.txt"                   line 17        
  2      "demo.txt"                     line 0
# %a 的位置有2中显示可能 
 %a : a=active,表示当前正在打开的文件
 #  ： 井号表示上一个打开的文件   

# 切换文件
/**
 * 1.如果需要指定切换文件的名称,则可以输入: ":open 需要打开的文件名称"
 */
 :open demo.txt
 // 打开之后再次查看可以看出我们上一个文件是 demo01.txt  当前活动文件是 demo.txt   
 :files
  1 #    "demo01.txt"                   line 17
  2 %a   "demo.txt"                     line 23
 
/**
 * 2.可以通过其它命令来切换上一个/下一个文件
 */   
:bn      # 切换到下一个文件  back next
:bp      # 切换到上一个文件  back prev(prev表示 Previous 以前的)  
```

### **编辑模式**

```php
i # 在光标所在字符前开始插入:insert(这个必须记住)
a # 在光标所在字符后开始插入:after (这个必须记住)
o # 在光标所在行的下面另起一行插入
I # 在光标所在行的行首开始插入 如果行首有空格则在空格之后插入
A # 在光标所在行的行尾开始插入
O # 在光标所在上的上面另起一行开始插入
S # 删除光标所在行并开始插入    
```

**实用功能**

```php
# 1.代码着色
# 案例
vim hello.c
# 添加编辑---开始   
#include <stdio.h>
    void main(){
    printf(hello world!)
 }
# 添加编辑---结束

# 关闭显示着色  syntax:语法  
:syntax on    # 显示
:syntax off    # 关闭显示
#--------------------------------------------------------------------------------------------------------------------     
# 2.vim中计算器的使用
当在编辑文件的时候突然需要使用计算器去计算一些公式,则此时需要用计算器,但是需要退出,vim自身集成了一个简易的计算器
# a.进入编辑模式 insert
# b.按下按键"ctrl+r",然后输入"=",此时光标会变到最后一行
# c.输入需要计算的内容"=2+3"回车键确认    
#-------------------------------------------------------------------------------------------------------------------- 
    
```



### **vim配置文件**

**说明**

> Vim 配置文件分为系统配置文件和用户配置文件：
>
> - 系统配置文件位于 Vim 的安装目录（默认路径为 /etc/.vimrc）；
> - 用户配置文件位于主目录 ~/.vimrc，即通过执行 `vim ~/.vimrc` 命令即可对此配置文件进行合理修改。通常情况下，Vim 用户配置文件需要自己手动创建。
>
> **简洁说明**
>
> 每个人使用编辑器例如**ide**都会有不同的设置习惯，编辑vim配置可以让我们构建自己喜欢的风格的配置**例如vim** 打开文件自动显示
>
> 行号

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](http://c.biancheng.net/view/3024.html)  [link](https://www.bookstack.cn/read/use_vim_as_ide/0.1.md) |

```php
# 说明
vim 是一款编辑器,编辑器也是有配置文件的.
vim配置有三种情况
   a.在文件打开的时候在末行模式下输入的配置(临时的) 例如：set nu 显示行号
   b.个人配置文件(~/.vimrc) 自己的家目录(此文件可能不会存在,如果没有可以自行新建)
   c.全局配置文件(vim自带:/etc/vimrc)
    
# 个人配置(~/.vimrc)
//① 新建好个人配置文件进入编辑    
[root@VM-16-5-centos ~]# ls .vimrc     // 查询是否存在
ls: cannot access .vimrc: No such file or directory  
[root@VM-16-5-centos ~]# touch .vimrc     //不存在新建
//②在配置文件中进行配置(当前用户永久)
 set nu        //比如显示行号
 syntax off     //vim编辑器不显示着色(不推荐使用)
 ......
# 全局配置文件(vim自带:/etc/vimrc)
    
```

**问题**

```php
# 1. 如果某个配置项,在个人配置文件与全局配置文件产生冲突的时候因该以谁为准?
//①实现开始(配置个人配置显示行号,全局配置不显示行号)
个人配置(~/.vimrc)下添加               set nu  
全局配置(vim自带:/etc/vimrc)下添加      set nonu    
//结果可以看出配置项会以个人配置为最高优先级   
    
//②实现继续二(配置个人配置不显示行号,全局配置显示行号)
个人配置(~/.vimrc)下添加               set nonu   
全局配置(vim自带:/etc/vimrc)下添加      set nu   
//结果可以看出配置项会以个人配置为最高优先级 
结论:如果针对于同一个配置项,个人配置文件中存在则以个人配置为准,如果个人配置文件中不存在这一项,则以全局配置文件为准
#----------------------------------------------------------------------------------

#2. 异常退出(非法退出:例如在编辑模式中强制关闭shell窗口,在编辑文件之后并没有正常的去wq（保存退出）而是遇到突然关闭终端或者断电的情况,则会显示下面的效果，这种情况称之为异常退出)
//再次重新编辑此文件就会显示以下(交换文件".demo.txt.swp"已经存在!)
.....    
Swap file ".demo.txt.swp" already exists!
[O]pen Read-Only, (E)dit anyway, (R)ecover, (D)elete it, (Q)uit, (A)bort:  # 只读，编辑，覆盖，删除，uit, bot:

// 解决办法(将交换文件删除即可(在编程过程中产生的临时文件))
 删除 .demo.txt.swp 交换文件
 ls .demo.txt.swp   //查询是否存在(可忽略)
 rm -rf .demo.txt.swp   //删除交换    
```

##  grep 

**资料**

| 名称 | 地址                                                         |
| ---- | ------------------------------------------------------------ |
| 博客 | [link](https://download.csdn.net/download/weixin_38713450/12841869?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-download-2%7Edefault%7ECTRLIST%7EPaid-1-12841869-blog-121425903.pc_relevant_multi_platform_featuressortv2dupreplace&depth_1-utm_source=distribute.pc_relevant.none-task-download-2%7Edefault%7ECTRLIST%7EPaid-1-12841869-blog-121425903.pc_relevant_multi_platform_featuressortv2dupreplace&utm_relevant_index=2) [link](https://www.runoob.com/linux/linux-comm-grep.html) |

```shell
# 只知道文字如何查询文件名
grep -rn "需要查询的文字" *
data/config/env.sh:2:.............        返回文件位置:文字存在的行数
```



## [别名机制](https://www.runoob.com/linux/linux-comm-alias.html)(创建自己的linux命令)

> <font color='red'>注意：创建别名之后重新进入终端生效</font>
>
> **作用:**相当于创建一些属于自己的自定义命令
>
> **例如:** 在`windows`下有`cls`命令,在Linux下可能因为没有这个命令而不习惯清屏,现在可以通过别名机制来解决
>
> 这个问题,可以自己创造出`cls`命令
>
> ###  别名机制依靠一个别名<font color='red'>映射</font>文件(当前用户家目录下):  ~/.bashrc
>
> > 像是一个连线题左右映射,左边可以关联右边,右边可以关联左边
>
> ```php
> 绿绿的        花朵
> 青青的        草地
> 蓝蓝的        天空    
> ```

```php
# 1. 编辑 .bashrc 手动添加命令(编辑之后wq保存,重新登录当前用户账户生效:退出shell重新登录)
vim ~/.bashrc
# 添加开始--------------------

# .bashrc
# User specific aliases and functions

alias rm='rm -i'
alias cp='cp -i'
alias mv='mv -i'
# 手动添加映射内容 cls命令
alias cls='clear'


# Source global definitions
if [ -f /etc/bashrc ]; then
        . /etc/bashrc
fi  
    
# 添加结束--------------------    
    
#--------------------------------------------end--------------------------------------------------------------     
```

[**使用alias命令添加操作别名**](https://www.coonote.com/linux/linux-cmd-alias.html)

 ```shell
 # 语法
 alias(选项)(参数)
 # 选项
 -p：打印已经设置的命令别名。
 # 设置别名
 alias 新的命令='原命令 -选项/参数' # 例子 alias cls='clear'
 # 删除别名
 unalias 别名名称
 ```



## linux自有服务

> 自有服务,即不需要用户独立去安装软件的服务,而是当系统安装好就可以直接使用的服务(内置)

### 运行模式(运行级别)

> 运行模式也可以称之为<font color='red'>运行级别</font>
>
> 在linux中存在一个进程:**init**(initialize:初始化的意思),进程id是**1**
>
> ```shell
> # 例如(显示表示系统启动之后第一个运行的进程)
> [root@VM-16-5-centos ~]# ps -ef | grep init
> ................
> ```
>
> 该进程存在一个对应的配置文件:**inittab**(<font color='red'>系统运行级别配置文件</font>)  **位置/etc/inittab**
>
> 根据上述的描述,可以得知.Centos6.5中存在7中运行级别/模式
>
> ```shell
> 0-表示关机级别(不要将默认的运行级别设置成0)
> 1-单用户模式
> 2-多用户模式,不带NFS(Network File System) 可以多用户但是没网
> 3-多用户模式,完全的多用户模式(不带桌面的,纯命令行模式)
> 4-没有被使用的模式(被保留模式)
> 5-X11,完整的图形化界面模式
> 6-表示重启级别(不要将默认的运行级别设置成0)
> ```
>
> 与该级别相关的几个命令:
>
> > 这些命令其实都是调用init 进程,将数字(运行级别)传递给进程,进程取读配置文件执行对应的操作.
>
> ```php
> init 0   表示关机
> init 3   表示切换到不带桌面的模式
> init 5   切换到图形界面
> init 6   重启电脑    
> ```
>
> 图形模式设置永久命令模式(将**/etc/inittab**文件中的**initdefault**值设置成3,然后重启操作系统(init 6 命令可以重启))

### 设置主机名

> hostname  # 查看主机名
> hostname  -f  # DQDN(全限定域名)
>
> #### 临时设置主机名(立竿见影) -重启服务器失效
>
> ```shell
> hostname	设置的主机名
> su          # 命令可以查看刷新效果
> ```
>
> ### 永久设置主机名(需要重启生效)
>
> ```shell
> # 查看对应配置文件
> [root@VM-16-5-centos ~]# cat /etc/sysconfig/network
> # Created by cloud-init on instance boot automatically, do not edit.
> #
> NETWORKING=yes
> HOSTNAME=你的主机名称    # 如果存在这个则需要修改  例如 HOSTNAME=yaoliuyang
> 
> # 修改linux服务器的host文件,将yaoliuyang指向本地(设置FQDN),不舍设置FQDN会怎么样(很多开源服务器软件例如Apache则无法启动或者出现报错,方便记忆看到主机名对其作用有一个初步判断,如果不设置则会影响本地的域名的解析(本地访问))
> /etc/hosts
> echo 127.0.0.1 yaoliuyang >> /etc/hosts
> ```
>

### [chkconfig:配置设置开机启动项](https://www.runoob.com/linux/linux-comm-chkconfig.html)

> 作用:作用相当于windows下**安全卫士**，**电脑管家**之内的安全辅助工具提供**开机启动项**的管理服务。
>
> 在linux下不是所有的软件安装完成之后都有开机启动服务,有的可能需要自己去添加.除此之外还可以查看和删除



```shell
#------------------------------------基本命令-------------------------------------------------------------------
使用方法: chkconfig [--list] [--type <type>] [name]
         chkconfig --add <name>  # 添加开机启动
         chkconfig --del <name>  # 删除开机启动
         chkconfig --override <name>
         chkconfig [--level <levels>] [--type <type>] <name> <on|off|reset|resetpriorities>  # 设置服务某个级别开机启动

#-------------------------------------查询开机启动------------------------------------------------------------------

# 开机启动服务查询(其中0-6表示各个启动级别，例如以network为例,其中3级别为开(on),则其表示在3启动形式下默认开机启动，5对应的也是关闭,则表示其在左面环境下也是开机启动)
[root@VM-16-5-centos ~]# chkconfig --list
.......
network        	0:off	1:off	2:on	3:on	4:on	5:on	6:off
.......

# 或者
[root@VM-16-5-centos ~]# chkconfig --list | grep network
network        	0:off	1:off	2:on	3:on	4:on	5:on	6:off

#----------------------------------------删除开机启动---------------------------------------------------------------
# 删除开机启动
chkconfig --del <name>
# 例如
[root@VM-16-5-centos ~]# chkconfig --del network

#------------------------------------------添加开机自动启动-------------------------------------------------------------
# 说明:并不是电脑所有安装的软件都有服务名称 service start <服务名称>
[root@VM-16-5-centos ~]# chkconfig --add <name>    【必须要保证服务正常运行,才可以添加】  默认加完之后所有级别全是关闭状态


#-------------------------------设置服务某个级别开机启动------------------------------------------------------------------------
chkconfig --level  连在一起的启动级别(12:1和2)  服务名  开/关(on/off)
# 例子:设置network 1和2级别开机启动 
[root@VM-16-5-centos ~]# chkconfig --level  12  network on

# 例子:设置network 5级别开机关闭 
[root@VM-16-5-centos ~]# chkconfig --level  5  network off
```



### ntp服务:设置同步时间管理

>作用:**ntp**主要作用于对<font color='red'>计算机的时间同步管理操作</font>
>
>时间是对服务器来说是很重要的,一般很多网站都需要读取服务器事件来记录相关信息,如果时间不准,则
>
>可能造成很大的影响

**资料**

| 名称              | 地址                               |
| ----------------- | ---------------------------------- |
| 中国ntp服务器节点 | [link](http://www.ntp.org.cn/pool) |

**扩展补充**

> [alipine时间同步](https://www.csdn.net/tags/MtzaIgwsMjQ0MzMtYmxvZwO0O0OO0O0O.html)
>
> linux 安装**ntpdate**  `yum install ntpdate`

```shell
# 例如我docker 容器里面的时间不准确
bash-5.0# date "+%Y-%m-%d %H-%M-%S"
2022-06-28 14-06-30
#------------------------------------------------------
# 同步服务器时间方式有两个:一次性同步(手动同步),通过服务自动同步

## 一次性同步(简单:不推荐因为是一次性的)  ntpdate 地址
ntpdate  120.25.115.20

## 设置事件同步服务  
#查看进程 ps -ef | grep ntpd
服务名: ntpd
# 启动服务 service ntpd start 或 /etc/init.d/ntpd start

# 设置开机启动
chkconfig --list
chkconfig --level  35  network on
```



### 防火墙服务

> 防火墙: 防范一些网络攻击.有<font color='red'>软件防火墙</font> 硬件防火墙之分
>
> 防火墙选择让请求通过，从而保证网络安全性
>
> [iptables](https://wenku.baidu.com/view/ec06bbf9a68da0116c175f0e7cd184254b351b1d.html)

```shell
# 在当前的centos6.5中防火墙有一个名称:iptables 【7.x中默认使用的事firewalld】  
#  ps -ef | grep iptables                           查看进程
# chkconfig  --list | grep iptables                 查看是否开机启动

# iptables 服务 启动/重启/停止
service iptables start/restart/stop           
/etc/init.d/iptables start/restart/stop           

#查看iptables状态(防火墙状态)
service iptables status  # 如果iptables没有启动,则提示服务没启动,则显示防火墙的相关的规则信息

# iptables 查看规则命令 
iptables -L -n       # 含义 -L 表示列出规则  -n表示将单词表达形式改成数字显示

# 简单设置防火墙
例如,需要设置运行80端口通过防火墙,则规则可以用以下的命令来设置
iptables -A INPUT -p tcp --dport 80 -j ACCEPT  # 允许访问80端口

iptables #主命令
-A add   #添加规则(z)
INPUT    # 进站请求 【出战 OUTPUT】
-p protocol  # 指定协议(icmp/tcp/udp)
--dport  # 指定端口号
-j  # 指定行为结果,允许(accept)/禁止(reject)

# 添加完成之后需要保存上一步设置防火墙操作(重要)
iptables save  # /etc/init.d/iptables save

#---------------------7.x防火墙操作--------------------------------
centos7.0  filewalld  # filewalld 防火墙的意思 d表示守护进程   

```







## 用户于用户组管理(重点)

>    Linux 系统是一个多用户多任务的操作系统,任何一个要使用系统资源的用户,都必须首先向系统管理员申请一个账号,
>
> 然后以这个账号的身份进入系统
>
>    用户账号一方面可以帮助系统管理员对使用系统的用户进行跟踪.并控制他们对系统的资源访问;另一方面也可以帮助用户组织文件.
>
> 并为用户提供安全性保护.
>
>    每个用户账号都拥有一个<font color='red'>唯一的用户名(不能重复)</font>      和各自的密码(密码可以重复)
>
>    用户在登录键入正确的用户名和密码后,就能够进入系统和自己的主目录.
>
> 
>
>    **要想实现用户账号的管理,要完成的工作主要有以下几个方面: **
>
> 1. 用户账号的添加,删除,修改以及用密码的管理
> 2. 用户组的管理

```php
# 注意三个文件:
/etc/passwd      // 存储用户的关键信息
/etc/group       // 存储用户组的关键信息
/etc/shadow      // 存储用户的密码信息 
```

### 用户管理

#### 添加用户

```php
 常用语法: #useradd 选项 用户名
    常用选项
        -g:            表示指定用户的用户组(主组:主要的组),选项的值可以使用户组的id,也可以是组名
        //linux允许用户有多个组但是只能有一个主组
        -G:            表示指定用户的用户组(附加组),选项的值可以使用户组的id,也可以是组名
        -u:  uid:      用户的id(用户的标识符),系统默认会从500之后分配uid,如果不想使用系统分配的,可以通过该选项自定义[类似于腾讯QQ的自选靓号情况] 
        -c: comment:   添加用户注释
案例: 创建用户 yaoliuyang,不带任何选项            
    useradd yaoliuyang  //创建
验证是否成功
    1.验证/etc/passwd的最后一行,查看是否有yaoliuyang的信息  
           cat /etc/passwd  //查看内容是否有创建的用户名
    2.验证是否存在家目录(在Centos下创建好用户之后随之产生一个同名   /home/yaoliuyang):
       ls /home
    3.        
 #----------------------------------------------------------
 注意：在不添加选项的时候,执行 useradd 之后会执行一系列的操作
       a.创建同名的家目录;
       b.创建同名的用户组;
 注意：查看用户的主组,可以查看passwd文件,查看附加组可以查看group文件(某个组后面:附加用户名称)
 #----------------------------------------------------------
 案例:添加选项,创建用户lisi,让李四属于501 主组,附加组500,自选靓号(uid)666
     useradd -g 501 -G 500 -u 666 lisi
```

#### [修改用户:usermod](https://www.runoob.com/linux/linux-comm-usermod.html)

> **usermod(user modify)** 用户编辑

```php
     常用语法: # usermod 选项 用户名
     常用选项
        -c <备注> 　修改用户帐号的备注文字。
        -g <群组> 　修改用户所属的群组。(表示指定用户的用户主组,选项值也可以是用户组的id,也可以是组名)
        -G <群组> 　修改用户所属的附加群组。(uid,用户的id,用户的标识符,系统会默认从500之后按顺序分配uid,如果不想使用系统分配的,可以通过该选项自定义 [类似于腾讯QQ自选靓号情况])
        -l <帐号名称> 　修改用户帐号名称。
        -L 锁定用户密码，使密码无效。
        -d 登入目录> 　修改用户登入时的目录。
        -e <有效期限> 　修改帐号的有效期限。
        -f <缓冲天数> 　修改在密码过期后多少天即关闭该帐号。
        -s <shell> 　修改用户登入后所使用的shell。
        -u <uid> 　修改用户ID。
        -U 解除密码锁定。
```

**案例**

```shell
# 案例一
# 修改用户主组&附加组(cat /etc/group 查看)
usermod -g 500 -G 501 yaoliuyang     # 修改yaoliuyang用户组为500,附加组为501
# ----------------------------------------------------------------------------
# 案例二
# 修改yaoliuyang 用户用户名,修改为 yaokun
usermod -l yaokun(新用户名) yaoliuyang(旧用户名) 
# ----------------------------------------------------------------------------
# 案例三  修改　修改用户登入后所使用的shell 例如 /sbin/nologin 禁止用户登录
[root@VM-16-5-centos ~]# cat /etc/passwd
apache:x:48:48:Apache:/usr/share/httpd:/sbin/nologin
yaoliuyang:x:1000:1000::/home/yaoliuyang:/bin/bash

[root@VM-16-5-centos ~]# useradd test

[root@VM-16-5-centos ~]# tail -1 /etc/passwd
test:x:1001:1001::/home/test:/bin/bash
# 修改shel
[root@VM-16-5-centos ~]# usermod -s /sbin/nologin test

[root@VM-16-5-centos ~]# tail -1 /etc/passwd
test:x:1001:1001::/home/test:/sbin/nologin
```

####  [设置密码](https://www.runoob.com/linux/linux-comm-passwd.html)

> Linux passwd命令用来更改使用者的密码,linux不允许没有密码的用户登录到系统
>
> 因此前面创建的用户目前都处于锁定状态,需要设置密码之后才能登录计算机

```shell
passwd 用户名 # 设置密码-设置完毕之后可以在  /etc/shadow 中查看区别

# 常用选项
-d 删除密码
-f 强迫用户下次登录时必须修改口令
-w 口令要到期提前警告的天数
-k 更新只能发送在过期之后
-l 停止账号使用
-S 显示密码信息
-u 启用已被停止的账户
-x 指定口令最长存活期
-g 修改群组密码
指定口令最短存活期
-i 口令过期后多少天停用账户
```

#### [删除用户](https://www.runoob.com/linux/linux-comm-userdel.html)

> 在设置用户密码之后可以登录账号,例如此处需要登录,
>
> **补充**
>
> 已经登录的用户(当前正在使用的账户)不能删除,可以使用**kill**命令关闭进程(ps -ef | grep 用户名),然后再次删除

```shell
# 命令
userdel [-r][用户帐号]

# 常用选项
-r 　删除用户登入目录以及目录中所有文件。
```

#### 认识**passwd**文件

```php
cat /etc/passwd
........
yaoliuyang:x:1001:1001::/home/yaoliuyang:/bin/bash
........

用户名:密码:用户id:用户组id:注释:家目录:解释器shell
# 用户名:创建新用户名称,后期登录的时候需要输入;
# 密码:此密码未知一般情况下都是"x",表示密码的占位
# 用户ID:用户的识别符;
# 用户组ID:该用户所属的主组ID         可以使用  cat /etc/group 命令查看组
# 注释：解释一下该用户是做什么用的;
# 家目录：用户登录系统之后默认的位置    
# 解释器shell：等待用户进入系统之后,用户输入指令之后,改解释器会手机用户输入的指令,传递给内核处理;(信息传递的桥梁)    
```

### 用户组管理

> ​       每个用户都有一个用户组,系统可以对一个用户组中的所有用户进行集中管理,不同<font color='green'>Linux</font>系统对用户组的规定有所不同,
>
> 如**linux**下的用户属于它同名的用户组,<font color='red'>这个用户组在创建用户时同时创建</font>
>
> ​           用户组的管理涉及用户组的添加,删除和修改.组的增加,删除和修改实际上就是对<font color='red'>/etc/group</font>文件的更新

**/etc/group**文件结构

> ## **结构**
>
> <font color='yellow'>用户组名:密码:用户组ID:组内用户</font> 
>
> **密码**: X表示占位符,虽然用户可以设置密码,但是绝大部分的情况下不设置密码
>
> 组内用户名:表示附加组是该组的用户名称;

```php
# cat /etc/group
........
sudo:x:27:yaoliuyang
yaoliuyang:x:1000:     
mysql:x:127:
uuidd:x:128:
saned:x:129:
.......
```



#### 用户组的添加

```shell
# 常用语法:
groupadd [-选项][组名]

# 参数说明
-g：指定新建工作组的 id； # 类似用户添加里的'-u',-g表示选择自己设置一个自定义的用户组ID数字,如果自己不指定,则默认从500之后递增;
-r：创建系统工作组，系统工作组的组 ID 小于 500；
-K：覆盖配置文件 /etc/login.defs；
-o：允许添加组 ID 号不唯一的工作组。
-f,--force: 如果指定的组已经存在，此选项将失明了仅以成功状态退出。当与 -g 一起使用，并且指定的 GID_MIN 已经存在时，选择另一个唯一的 GID（即 -g 关闭）。
```

**案例**

```shell
# 使用groupadd指令创建一个新的用户组,命名为Administrators
[root@VM-16-5-centos ~]# groupadd Administrators
# 查看创建的用户组
[root@VM-16-5-centos ~]# tail -1 /etc/group
Administrators:x:1001:
```

#### [用户组的编辑](https://www.runoob.com/linux/linux-comm-groupmod.html)

**groupmod 语法**

```shell
常用语法: groupmod 选项 用户组名

常用选项:
 -g   <群组识别码> 　设置欲使用的群组识别码。 # 类似用户添加里的'-u',-g表示选择自己设置一个自定义的用户组ID数字;
 -o 　重复使用群组识别码。
 -n   <新群组名称> 　设置欲使用的群组名称。# 类似于用户修改"-l",表示设置新的用户组的命令
 
 # groupmod -h
 选项:
  -g, --gid GID                 将组 ID 改为 GID
  -h, --help                    显示此帮助信息并推出
  -n, --new-name NEW_GROUP      改名为 NEW_GROUP
  -o, --non-unique              允许使用重复的 GID
  -p, --password PASSWORD       将密码更改为(加密过的) PASSWORD
  -R, --root CHROOT_DIR         chroot 到的目录
  -P, --prefix PREFIX_DIR       prefix directory where are located the /etc/* files
```

**案例**

```shell
# 案例一  修改Administrators用户组,将组ID从1001改成1002,将名称改为admins

# 查看对应的用户组
[root@VM-16-5-centos ~]# tail -1 /etc/group
Administrators:x:1001:
# 修改
[root@VM-16-5-centos ~]# groupmod -g 1002 -n admins Administrators
# 再次查看已修改的用户组
[root@VM-16-5-centos ~]# tail -1 /etc/group
admins:x:1002:
#-------------------------案例一结束----------------------------------------
```

#### 用户组的删除

```shell
groupdel [群组名称]

# 案例 删除admins用户组	
[root@VM-16-5-centos ~]# tail -2 /etc/group
dockerroot:x:991:
admins:x:1002:

[root@VM-16-5-centos ~]# groupdel admins

[root@VM-16-5-centos ~]# tail -2 /etc/group
cgred:x:992:
dockerroot:x:991:

# 注意:当如果需要删除一个组,但是这个组是某个用户的主组时,则不允许删除;如果确实需要删除,则先从组内移出所有用户
```



## 网络设置

> 首先知道网卡配置文件位置:**/etc/sysconfig/network-scripts/** <font color='yellow'>目录下全是网卡配置文件</font>

```shell
[root@VM-16-5-centos ~]# ls /etc/sysconfig/network-scripts/
ifcfg-eth0  ifdown-bnep  ifdown-ipv6  ifdown-ppp     ifdown-Team      ifup          ifup-eth   ifup-isdn   ifup-post    ifup-sit       ifup-tunnel       network-functions
ifcfg-lo    ifdown-eth   ifdown-isdn  ifdown-routes  ifdown-TeamPort  ifup-aliases  ifup-ippp  ifup-plip   ifup-ppp     ifup-Team      ifup-wireless     network-functions-ipv6
ifdown      ifdown-ippp  ifdown-post  ifdown-sit     ifdown-tunnel    ifup-bnep     ifup-ipv6  ifup-plusb  ifup-routes  ifup-TeamPort  init.ipv6-global  route6-eth0
# 这两个就是网卡配置文件(对应 ifconfig)
ifcfg-eth0  ifcfg-lo

# 在目录中网卡的配置文件命名格式:ifcfg-网卡名称
[root@VM-16-5-centos ~]# cat /etc/sysconfig/network-scripts/ifcfg-eth0 
# Created by cloud-init on instance boot automatically, do not edit.
#
BOOTPROTO=dhcp                 # 地址的分配方式,DHCP表示动态主机分配协议 
DEVICE=eth0
HWADDR=52:54:00:15:2e:ef       # 硬件地址,MAC地址 
ONBOOT=yes                     # 是否开机启动
PERSISTENT_DHCLIENT=yes
TYPE=Ethernet                      
USERCTL=no
#-----------------------------------------------------------------------------------------------------
# 如果后续需要重启网卡怎么去操作呢?

# 1.service network restart (不推荐使用)

[root@VM-16-5-centos ~]# service network restart
Restarting network (via systemctl):                        [  OK  ]

# 2. 此处的重启网卡命令还可以使用(强烈推荐):    /etc/init.d/network restart

在有的分支版本中可能没有service命令快速操作服务,但是有一个共性的目录:/etc/init.d  这个目录中访着很多对服务的快捷方式

[root@VM-16-5-centos ~]# ls /etc/init.d/
functions  mst  netconsole  network  README
# 重启网卡
[root@VM-16-5-centos ~]# /etc/init.d/network restart
Restarting network (via systemctl):                        [  确定  ]
#----------------------------------------------------------------------------------------------------

# 扩展:如果修改网卡的配置文件,但是配置文件的目录层次很深,此时可以在浅的目录中创建一个快捷方式,方便以后去查找

ln -s  原始文件路径  快件方式的路径

[root@VM-16-5-centos ~]# ln -s /etc/sysconfig/network-scripts/ifcfg-eth0 ~/ifcfg-eth0
[root@VM-16-5-centos ~]# ls
ifcfg-eth0
```

**扩展:如何重启单个网卡**

```shell
# 停止某个网卡
[root@VM-16-5-centos ~]# ifdown -h
用法：ifdown <设备名:网卡名>

# 开启某个网卡
[root@VM-16-5-centos ~]# ifup -h
用法：ifup <设备名:网卡名>

# 例子:需要停止-启动(重启) eth0网卡,则可以输入
ifdown eth0 (慎用,最好下面的命令连着一起用,不然直接断网只能 总后台重启)
ifup eth0
```

## rpm管理(centos系统)

> Red-Hat Package Manager（红帽软件包管理器）的缩写
>
> 作用: rmp的作用类似于windows上电脑管家中的**软件管理**,主要作用是对linux服务器
>
> 上的软件包进行对应管理操作,管理分为:**查询,卸载,安装(更新)**

**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络资料 | [百度百科-rpm](https://baike.baidu.com/item/RPM/3794648?fr=aladdin) |

**常用命令**

```shell
# -----------------查询----------------------
# 查询   某个软件的安装情况
rpm -qa | grep 关键词
    -q query 查询
    -a all   全部
# -----------------卸载----------------------
# 卸载某个软件
rmp -e 软件的名称

# 如果卸载软件但是有依赖关系(依赖另一个软件)不给卸载,所以当存在依赖关系的时候但是又不想去解决这个问题的时候(不想卸载依赖软件)
rmp -e 软件包名  --nodeps            
    --nodeps  (no  dependencies)  忽略关联
    
# -----------------安装----------------------
要想装软件,和windows下一样,先得找到安装包
  
  rmp -ivh 软件包名称完整
      -i install  #安装
      -v view     #显示进度
      -h          #表示以#(井号形式显示进度条)
```

## [**cron/crontab计划任务**](https://www.runoob.com/linux/linux-comm-crontab.html)

> 设置定时任务可以从网络上查询这个比较简单不予记录,这里主要研究以下权限问题

```shell
# Crontab 权限问题:本身是任何用户都可以创建自己的计划任务
但是超级管理员可以通过配置来设置某些用户不允许设置计划任务
  配置文件位于: /etc/cron.deny  里面写用户名:一行一个就可以了
  
  # 例子 /etc/cron.deny(黑名单不允许名单里面的人编辑定时任务)  没有此文件直接创建即可
    vim /etc/cron.deny 
      .........
      1 用户名称
      2 yaoliuyang
      .........
# 切换用户后会发现编辑crontab 会发现没有权限      
[root@VM-16-5-centos ~]# su yaoliuyang
[yaoliuyang@VM-16-5-centos root]$  crontab -e
You (yaoliuyang) are not allowed to use this program (crontab)
See crontab(1) for more information
#------------白名单 允许名单内的人编辑--------------
文件位于: /etc/cron.allow  如果不存在自己创建即可

# 注意:白名单的优先级高于黑名单,如果一个用户同时存在两个名单(黑白名单都有)中,则会被默认允许创建计划任务
```

## linux权限管理(重点)

> Linux的权限操作于**用户**,**用户组**是兄弟操作
>
> 权限概述:Linux系统一般将文件可存/取访问的身份分为2个类别:<font color='red'>owner(所有者),group(所有组),others(其他的)</font>,且三种
>
> 身份各有 <font color='red'>read,write,execute</font>等权限

### **权限介绍**

> 什么是权限?
>
> 在**多用户**(可以不同时) 计算机系统的管理中,权限是指某个特定的系统资源使用<font color='red'>权利</font>,像是文件夹,特定系统
>
> 指定的使用或存储量的限制

```shell
#在Linux中分别有 读,写,执行 权限
读权限:
   对于用户来说,读权限影响用户是否能够列出目录结构
   对于文件来说，读权限影响用户是否可以查看文件内容
   
写权限:
   对于文件夹来说，写权限影响用户是否可以在文件夹下“创建/删除/复制到/移动到”文档
   对于文件来说，写权限影响用户是否可以编辑文件内容
   
执行权限:
   一般对于文件来说,特别脚本文件。
```



### **身份介绍**

> <font color='red'>**Owner**</font>身份(文件所有者)
>
> 由于**Linux**是多用户,多任务的操作系统,因此可能常常有多人同时在某台主机上工作,但每个人均可以
>
> 在主机上设置文件的权限,让其成为个人的**私密文件**,即个人所有者,因为设置了适当的文件权限,除本人(文件所有者)
>
> 之外的用户无法查看文件内容
>
> ## 例子
>
> 例如某个MM给你发了一封Email情书,你将情书转为个人文件之后存档在自己的主文件夹中。问了不让别人看到情书
>
> 内容，你就能利用所有者的身份去设置文件的适当权限，这样即使你的情敌想偷看你的情书内容也是做不到的
>
> <font color='red'>**Group**</font>身份 与文件所有者的同组的用户
>
>    与文件所有者同组最有用的功能就体现在多个团队在同一台主机上开发资源的时候。例如主机上有**A,B**俩个
>
> 团体，**A**中有**a1,a2,a3**三个成员,**B**中有**b1,b2**两个成员,这两个团体要共同完成一份报告**F**。由于设置了适当的
>
> 权限，**A,B**两个团体中的成员都能互相修改对方的数据,但是团体**C**成员则不能修改**F**的内容,甚至连查看的权限都没有
>
> 同时,团体的成员也能设置自己的私密文件,让团队的其它成员也读取不了文件数据。在linux中，每个账户支持多个用户
>
> 组。例如用户**a1,b1**即可属于**A**用户组,也能属于**B**用户组【主组和附加组】
>
> <font color='red'>**Others**</font>身份(其他人)
>
>    这个是个相对概念。打个比方，大明、二明、小明一家三兄弟住在一间房，房产证上的登记者是大明，那么，大明一家就是一个用户组，这个组有大明、二明、小明三个成员；另外有个人叫张三，和他们三没有关系，那么这个张三就是其他人了。
> 同时，大明、二明、小明有各自的房间，三者虽然能自由进出各自的房间，但是小明不能让大明看到自己的情书、日记等，这就是文件所有者（用户）的意义。
>
> <font color='red'>**Root**</font>用户(超级用户)
>
> 在linux中，还有一个神一样的存在的用户,这就是root用户，因为在所有用户中它拥有最大的权限,所以管理者普通用户



### Linux的权限介绍

> 要设置权限,就需要知道文件的一些基本属性和权限的分配规则,在Linux中,ls命令常用查看文件的属性,用于显示文件的文件
>
> 名和相关属性

**查看文件属性**

```shell
ls -l 路径 # 显示的结果如下图
```

> 十位字符表示含义:
>
> 第一位:表示文档类型,取值常见的有**d 表示文件夹,-表示文件,l表示软连接,s表示套接字**
>
> 第 2-4位 表示文档所有者所有者 的权限情况,第二位表示读权限的情况 取值有**r,-** 第三位表示写权限
>
> 情况,w表示可写,-表示不可写, 第四位表示执行权限的情况,取值有**x,-**
>
> 第 5-7位: 表示与所有者同在一个组的用户的权限情况
>
> 第8-10位 表示除了上面的前两部分用户之外的其他用户的权限情况
>
> **权限分配中,均是rwx的三个参数组合,且<font color='red'>位置顺序不会变化</font>.没有对应权限就用-替代**

![img](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/file-llls22.jpg)

### 权限设置

> 语法:chmod [选项] [权限模式]  文档地址
>
> 注意事项: 
>
> ​     常用选项 
>
> ​           -R  递归设置权限(当文档类型为文件夹的时候)
>
> ​     权限模式: 就是该文当需要设置的权限信息
>
> ​     文档:可以是文件,也可以是文件夹,可以是相对路径也可以是绝对路径
>
> 注意点: 如果想要给一个文档设置权限,操作者要么是**root**用户,要么是文件的**所有者** 

**设置权限命令**

#### 符号模式(字母形式)

> 使用符号模式可以设置多个项目：who（用户类型），operator（操作符）和 permission（权限），每个项目的设置可以用逗号隔开。 命令 chmod 将修改 who 指定的用户类型对文件的访问权限，用户类型由一个或者多个字母在 who 的位置来说明，如 who 的符号模式表所示:

| who  | 用户类型 | 说明                                                         |
| :--- | :------- | :----------------------------------------------------------- |
| `u`  | user     | 文件所有者                                                   |
| `g`  | group    | 文件所有者所在组                                             |
| `o`  | others   | 所有其他用户                                                 |
| `a`  | all      | 所用用户, 相当于 *ugo*                                       |
|      |          | <font color='red'>如果设置权限的时候不指定给谁设置,则默认给所有用户设置</font> |

operator 的符号模式表:

| Operator | 说明                                                   |
| :------- | :----------------------------------------------------- |
| `+`      | 为指定的用户类型增加权限                               |
| `-`      | 去除指定用户类型的权限                                 |
| `=`      | 设置指定用户权限的设置，即将用户类型的所有权限重新设置 |



####  permission 的符号模式表(权限字符):

| 模式 | 名字         | 说明                                                         |
| :--- | :----------- | :----------------------------------------------------------- |
| `r`  | 读           | 设置为可读权限                                               |
| `w`  | 写           | 设置为可写权限                                               |
| `x`  | 执行权限     | 设置为可执行权限                                             |
| `X`  | 特殊执行权限 | 只有当文件为目录文件，或者其他类型的用户有可执行权限时，才将文件权限设置可执行 |
| `s`  | setuid/gid   | 当文件被执行时，根据who参数指定的用户类型设置文件的setuid或者setgid权限 |
| `t`  | 粘贴位       | 设置粘贴位，只有超级用户可以设置该位，只有文件所有者u可以使用该位 |

#### 数字形式

**八进制语法**

> chmod命令可以使用八进制数来指定权限。文件或目录的权限位是由9个权限位来控制，每三位为一组，它们分别是文件所有者（User）的读、写、执行，用户组（Group）的读、写、执行以及其它用户（Other）的读、写、执行。历史上，文件权限被放在一个比特掩码中，掩码中指定的比特位设为1，用来说明一个类具有相应的优先级。

| #    | 权限           | rwx  | 二进制 |
| :--- | :------------- | :--- | :----- |
| 7    | 读 + 写 + 执行 | rwx  | 111    |
| 6    | 读 + 写        | rw-  | 110    |
| 5    | 读 + 执行      | r-x  | 101    |
| 4    | 只读           | r--  | 100    |
| 3    | 写 + 执行      | -wx  | 011    |
| 2    | 只写           | -w-  | 010    |
| 1    | 只执行         | --x  | 001    |
| 0    | 无             | ---  | 000    |



#### **案例**

```shell
# 需要给 a.txt设置权限,要求所有者拥有全部的权限,同组用户拥有读和执行权限,其他用户只读权限
[root@VM-16-5-centos ~]# ls -l
总用量 0
-rw------- 1 root root 0 6月  30 21:28 a.txt
# 三种方式(符号模式设置)
[root@VM-16-5-centos ~]# chmod u+x,g+rx,o+r a.txt          # 权限逗号分割
# -------------------------------------------------------------

[root@VM-16-5-centos ~]# chmod u-x,g-rx,o-r a.txt          #删除上一步赋值的权限  

# --------------------------------
[root@VM-16-5-centos ~]# chmod u=rwx,g=rx,o=r a.txt

# --------------------------------
[root@VM-16-5-centos ~]# chmod a=---                       # 设置全部没有权限 a= 所有
```

### 属主与属组设置

> 属主:所属的用户(文件的主人) 
>
> 属组:所属的用户组
>
> ## 例
>
> [root@VM-16-5-centos ~]# ll
> 总用量 0
> -rw------- 1 yaoliuyang yaoliuyang 0 6月  30 21:28 a.txt       
>
> 第一个  yaoliuyang 代表属主(文件或文件夹属于的主人)
>
> 第二个 yaoliuyang 代表属组(文件或文件夹属于那个用户组)
>
> 这两个信息是文档创建的时候会使用创建者的信息(用户名,用户所属的<font color='red'>主组</font>名称)
>
> <font color='yellow'>如果说有时候去删除某个用户,则该用户对应的文档的属主和属组信息就需要修改</font>

#### chown(**change owner**）掌握

> 命令用于设置文件所有者和文件关联组的命令
>
> **作用**: 更改文档的所属用户
>
> **语法**:chown username 文档路径
>
> **参数** :
>
> - user : 新的文件拥有者的使用者 ID
> - group : 新的文件拥有者的使用者组(group)
> - -c : 显示更改的部分的信息
> - -f : 忽略错误信息
> - -h :修复符号链接
> - -v : 显示详细的处理信息
> - -R : 处理指定目录以及其子目录下的所有文件(<font color='red'>如果是文件请用大R递归指定文件夹下的文件或目录,看情况使用如果需要递归请自行加上参数</font>)       
> - --help : 显示辅助说明
> - --version : 显示版本

**示例**

```shell
# 更改文件所属主&所属组       
chown  所属主名称:所属组名称  文件名称

[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 yaoliuyang yaoliuyang 0 6月  30 21:28 a.txt
[root@VM-16-5-centos ~]# chown root:root a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root root 0 6月  30 21:28 a.txt


# 只更改所属主
chown 所属主名称 a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 yaoliuyang yaoliuyang 0 6月  30 21:28 a.txt
[root@VM-16-5-centos ~]# chown root a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root yaoliuyang 0 6月  30 21:28 a.txt

# 只更改所属组
chown :所属组名称 a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root yaoliuyang 0 6月  30 21:28 a.txt
[root@VM-16-5-centos ~]# chown :root a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root root 0 6月  30 21:28 a.txt
```

#### **chgrp**(change group) 了解

> 作用:更改文档的所属用户组
>
> 语法:chgrp -R username(用户组名称) 文档的路径

**案例**

```shell
# 更改用户的所属组
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root root 0 6月  30 21:28 a.txt
[root@VM-16-5-centos ~]# chgrp yaoliuyang a.txt 
[root@VM-16-5-centos ~]# ll
总用量 0
-rw------- 1 root yaoliuyang 0 6月  30 21:28 a.txt
```

## 扩展:

### 问题:**reboot,shutdown,init,halt,user**管理,在普通用户身份上都是操作不了,但是有些特殊情况下有需要有执行权限。有不可能让**root**用户把自己的密码告诉普通用户，这个问题该怎么解决

> 该问题是可以被解决的，可以使用**sudo**(switch user do) 命令来进行权限设置。sudo 可以让管理员(root)用户事先定义某些特殊命令谁可以执行。
>
> <font color='yellow'>sudo 中是没有除root之外用户的规则,要想使用规则先配置sudo</font>
>
> **配置文件位置**<font color='red'>/etc/sudoers</font>

```shell
# 配置 sudo 文件 请使用 visudo 命令,打开之后其使用方法和vim一致

visudo     # 改命令直接编辑配置/etc/sudoers文件
...
root    ALL=(ALL)       ALL
...
root 表示用户名,如果是用户组则可以写成"%组名"
ALL：表示允许登录的主机(地址白名单)
(ALL):表示以谁的身份执行,ALL表示root身份
All: 最后面的那个ALL,可以执行的命令,多个命令可以使用"," 分割
```

####  **新增权限示例**

```shell
visudo
shitft + g  (或大G) 跳转到末行添加

## 案例:本身yaoliuyang用户不能执行添加用户，要求使用sudo配置，将其设置为可以添加用户,并且可以修改密码(但是不能修改root用户密码)
# 注意:在写sudo规则的时候不建议写直接形式的命令，而是写命令的完整路径路径可以使用which命令(语法:which 指令名称)来查看,或者直接vim命令模式下执行外部名 :!which useradd

...
## 自定义新增用户权限
yaoliuyang    ALL=(ALL)     /usr/sbin/useradd,!/usr/bin/passwd,/usr/bin/passwd [A-Za-z]*,!/usr/bin/passwd root,/usr/bin/docker
...

# 切换用户
[root@VM-16-5-centos ~]# su yaoliuyang
# 正常执行命令报错没有权限
[yaoliuyang@VM-16-5-centos /]$ docker ps -a
Got permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get http://%2Fvar%2Frun%2Fdocker.sock/v1.26/containers/json?all=1: dial unix /var/run/docker.sock: connect: permission denied
# 加上sudo命令执行（第一次需要输入当前用户密码进行确认的操作，有效期五分钟）
[yaoliuyang@VM-16-5-centos /]$ sudo docker ps -a
CONTAINER ID        IMAGE                                   COMMAND             CREATED             STATUS              PORTS                                          NAMES
9412b4c5028d        hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"           3 weeks ago         Up 3 days           0.0.0.0:9501->9501/tcp, 0.0.0.0:1997->80/tcp   laravel_study
b9a921d5e83c        hyperf/hyperf:7.4-alpine-v3.11-swoole   "/bin/sh"           3 weeks ago         Up 3 days           0.0.0.0:9505->9505/tcp, 0.0.0.0:2022->80/tcp   php_demo

# 禁止普通用户更改root密码 ！表示禁止
## !/usr/bin/passwd,/usr/bin/passwd [A-Za-z]*,!/usr/bin/passwd root      # 先禁止(!)所有用户，然后匹配正则用户，最后再禁止root用户
[yaoliuyang@VM-16-5-centos /]$ sudo passwd root
[sudo] yaoliuyang 的密码：
对不起，用户 yaoliuyang 无权以 root 的身份在 VM-16-5-centos 上执行 /bin/passwd root。
```

#### **补充:在普通用户下怎么查看自己具有那些特殊权限呢?**

> **sudo -l**   
>
> sudo 不是任何Linux分支都有的命令，常见centos与ubuntu都存在sudo命令。

```shell
[yaoliuyang@VM-16-5-centos /]$ sudo -l

用户 yaoliuyang 可以在 VM-16-5-centos 上运行以下命令：
    (ALL) /usr/sbin/useradd, /usr/bin/passwd [A-Za-z]*, !/usr/bin/passwd root, /usr/bin/docker

```

## 远程系统之间复制文件

### scp 命令

**资料**

| 名称            | 地址                                                     |
| --------------- | -------------------------------------------------------- |
| 菜鸟教程scp命令 | [link](https://www.runoob.com/linux/linux-comm-scp.html) |

SCP（Secure Copy）是Linux中用于在本地系统和远程系统之间复制文件的命令。它通过SSH（Secure Shell）协议进行加密传输，确保文件传输的安全性。

SCP命令的基本语法为：

```shell
scp [选项] [源文件路径] [目标文件路径]
```

其中，选项可以是以下之一：

- `-P`：指定远程SSH服务器的端口号（默认为22）；
- `-r`：递归复制目录和文件；
- `-v`：显示命令的详细输出；
- `-p`：保留原始文件的修改时间和权限。

源文件路径和目标文件路径可以是本地路径或远程路径，远程路径需使用以下格式：`[user@]host:路径`。

以下是几个示例：

1. 从本地复制文件到远程服务器：

```shell
scp /本地路径/文件名 用户名@远程主机IP地址或主机名:/远程路径/文件名
```

1. 从远程服务器复制文件到本地：

```shell
scp 用户名@远程主机IP地址或主机名:/远程路径/文件名 /本地路径/文件名
```

1. 从本地复制目录及其内容到远程服务器：

```shell
scp -r /本地路径/目录名 用户名@远程主机IP地址或主机名:/远程路径/目录名
```

1. 从远程服务器复制目录及其内容到本地：

```shell
scp -r 用户名@远程主机IP地址或主机名:/远程路径/目录名 /本地路径/目录名
```

请根据实际情况替换示例中的路径、用户名和主机信息。记得在使用scp命令时，需要确保本地和远程的文件/目录路径的访问权限正确。

### rsync 命令

Rsync是一种用于在本地系统和远程系统之间同步文件和目录的命令。它具有强大且高效的文件复制功能，可以选择性地更新或备份文件，也可以在不同系统之间同步文件。

Rsync命令的基本语法为：

```
rsync [选项] 源路径 目标路径
```

其中，选项可以是以下之一：

- `-a`：归档模式，保留文件的所有属性，包括权限、所有者、组、时间戳等。
- `-v`：详细输出，显示复制过程中的详细信息。
- `-r`：递归复制目录及其内容。
- `-z`：压缩传输，使用压缩算法减少数据传输量。
- `-P`：显示进度条，并支持中断后继续传输。
- `--delete`：删除目标路径上与源路径不一致的文件。

以下是几个示例：

1. 从本地同步文件到远程服务器：

```
rsync [选项] /本地路径/文件名 用户名@远程主机IP地址或主机名:/远程路径/文件名
```

1. 从远程服务器同步文件到本地：

```
rsync [选项] 用户名@远程主机IP地址或主机名:/远程路径/文件名 /本地路径/文件名
```

1. 从本地同步目录及其内容到远程服务器：

```
rsync [选项] /本地路径/目录名 用户名@远程主机IP地址或主机名:/远程路径/目录名
```

1. 从远程服务器同步目录及其内容到本地：

```
rsync [选项] 用户名@远程主机IP地址或主机名:/远程路径/目录名 /本地路径/目录名
```

请根据实际情况替换示例中的路径、用户名和主机信息。记得在使用rsync命令时，需要确保本地和远程的文件/目录路径的访问权限正确。

## screen多终端命令

第三方博客参考  https://www.mintimate.cn/2021/09/02/howToUseScreen/

[**screen 菜鸟教程**](https://www.runoob.com/linux/linux-comm-screen.html)

> **screen**是一个可以在终端中创建多个虚拟终端会话的命令行工具。它可以让你在一个物理终端窗口中同时运行多个程序，并且可以自由地在这些程序之间切换。
>
> 举个例子，假设你正在编写一个需要长时间运行的程序，但是你又不想关闭终端窗口，这时候就可以使用Screen来创建一个会话，将程序放入该会话中运行。当你需要暂时离开电脑时，可以使用分离功能将该会话从当前终端窗口分离出去，让它在后台继续运行。当你回来时，只需要重新连接到该会话，就可以继续查看程序的输出或者对其进行操作。
>
> 另外，Screen还支持列出所有当前存在的会话、关闭会话、杀死会话等操作。这些功能可以帮助你更好地管理多个会话，提高工作效率。
>
> **举个栗子**
>
> 假如我们想安装一个lamp一键安装包,这个sh命令要执行很长时间,万一中间不小心shell连接断了或者不小心断电了  shell终端就会中断执行
>
> 我们执行了半天的命令都要失效了 这种时候就可以用**screen** 开启一个终端取执行这个命令即使断电断网也会再shell后台继续执行我们的
> 命令
>
> **screen命令解释**
>
> **参数说明**：
>
> - -A 　将所有的视窗都调整为目前终端机的大小。
> - -d<作业名称> 　将指定的screen作业离线。
> - -h<行数> 　指定视窗的缓冲区行数。
> - -m 　即使目前已在作业中的screen作业，仍强制建立新的screen作业。
> - -r<作业名称> 　恢复离线的screen作业。
> - -R 　先试图恢复离线的作业。若找不到离线的作业，即建立新的screen作业。
> - -s<shell> 　指定建立新视窗时，所要执行的shell。
> - -S<作业名称> 　指定screen作业的名称。
> - -v 　显示版本信息。
> - -x 　恢复之前离线的screen作业。
> - -ls或--list 　显示目前所有的screen作业。
> - -wipe 　检查目前所有的screen作业，并删除已经无法使用的screen作业。

```shell
# 可以创建一个名为my_session的新会话 创建新的会话：使用screen -S session_name命令，这里的session_name可以自定义为你希望设定的会话名称。例如，键入screen -S my_session就可以创建一个名为my_session的新会话。

screen -S my_session

# 分离会话：当你需要暂时离开电脑时，可以使用Ctrl-a + Ctrl-d将当前会话从屏幕分离，使其在后台继续运行

# 重新连接会话：当你回来后，可以通过输入screen -r session_name命令重新连接到之前创建的会话，继续查看程序的输出或者对其进行操作。

# 列出所有会话：使用screen -ls命令，可以列出当前所有存在的会话

# 关闭会话：如果你想要结束某个会话，可以使用screen -X -S session_name quit命令来关闭它。

# 杀死会话：对于一些无法正常结束的会话，可以使用screen -X -S session_name kill命令来强制结束它。
```

## 后台启动命令 nohup  

[**菜鸟教程-nohup**](https://www.runoob.com/linux/linux-comm-nohup.html)

> 在 Linux 中，nohup 表示 "No Hang Up"（不挂断），它是一个用于在后台运行命令并忽略挂起信号的工具
>
> nohup命令是Linux系统中用于在后台运行程序并忽略挂起信号的命令。它可以保证即使用户退出登录或者关闭终端，程序仍然继续运行。

```shell
# 基本语法
nohup command [arguments] &
```

> 详解
>
> 1. `command`：需要执行的命令，例如：`python my_script.py`。
> 2. `[arguments]`：可选参数，传递给命令的参数。
> 3. `&`：将命令放入后台运行。

使用示例：

1. 安装nohup命令： 对于大多数Linux发行版，nohup命令已经预装。如果没有，可以使用包管理器进行安装。例如，在基于Debian的系统（如Ubuntu）上，可以使用以下命令安装：

   ```shell
   sudo apt-get install nohup
   ```

  2 . 使用nohup命令运行程序： 假设我们有一个名为`my_script.py`的Python脚本，我们希望在后台运行它，即使关闭终端也不会停止。可以使用以下命令：

```shell
nohup python my_script.py &
```

3. 查看nohup命令的输出： 默认情况下，nohup命令的输出会被重定向到名为`nohup.out`的文件中。可以使用以下命令查看输出：

```shell
tail -f nohup.out
```

4. 停止nohup命令： 如果需要停止nohup命令，可以使用`ps`和`kill`命令找到进程ID（PID），然后杀死进程。首先，使用以下命令查找进程ID：

```shell
ps aux | grep my_script.py

# 然后，使用以下命令杀死进程（将<PID>替换为实际的进程ID）：
kill <PID>
```

## sshd

> SSHD是**Linux系统下的一个关键服务，主要用于提供安全的远程登录和文件传输功能**。而它的配置文件则包含了**服务行为控制、安全设置、性能优化等**众多配置选项，对于系统管理员来说理解这些配置至关重要。

**参考资料**

| name        | url                                                          |
| ----------- | ------------------------------------------------------------ |
| 网络博客    | [1](https://www.cnblogs.com/uthnb/p/9367875.html) [2](https://blog.csdn.net/dengjin20104042056/article/details/100045269) [3](https://blog.csdn.net/weixin_40228200/article/details/120914453) |
| openssh官网 | [link](https://www.openssh.com/)                             |
| sshd手册    | [link](https://man.openbsd.org/sshd.8)  [sshd_config](https://man.openbsd.org/sshd_config.5) |

### Linux sshd详解：

1. **基本概念**：
   - SSH（Secure Shell）是一个网络协议，用于在不安全的网络中提供安全的通道进行远程登录和其他安全网络服务。
   - SSHD是SSH的服务器端程序，它监听来自客户端的连接请求，并处理加密认证和会话建立过程。
   - SSHD服务通常运行在端口22上，使用TCP协议来传输数据。
2. **安装与启动**：
   - 在大多数Linux发行版上，SSHD可以通过包管理器如`apt`或`yum`来安装。
   - 安装后，可以使用`systemctl`命令来启动、停止、重启或重新加载SSHD服务的配置。
   - 例如，`sudo systemctl start sshd`可以开启SSHD服务。
3. **安全性**：
   - SSHD利用密钥对认证提供了比传统密码更安全的登录方式。
   - 它支持多种加密算法来保护数据传输的安全。
   - 管理员可以限制哪些用户可以远程登录，甚至可以根据时间、IP地址等条件来限制访问。
4. **配置管理**：
   - SSHD的主要配置文件位于`/etc/ssh/sshd_config`，这里定义了所有服务器端的行为和规则。
   - 修改配置文件后，需要用`systemctl reload sshd`命令重新加载配置，无需重启整个服务。
   - 配置文件中的每个参数都有详细的注释，帮助管理员理解其功能。
5. **日志记录**：
   - SSHD将其操作记录在系统日志文件中，通常是`/var/log/auth.log`或`/var/log/secure`。
   - 通过查看这些日志，管理员可以监控非法访问尝试以及其他与SSH服务相关的活动。

### Linux sshd配置文件详解：

1. **配置文件概述**：
   - `Port`指令定义了SSHD监听的端口号，默认为22。
   - `Protocol`指定使用的SSH协议版本，建议使用更现代的版本2来增强安全性。
   - `PermitRootLogin`设置是否允许root用户登录，从安全角度考虑，通常建议设置为no。
2. **身份验证设置**：
   - `PasswordAuthentication`和`PermitEmptyPasswords`控制是否允许基于密码的身份验证。出于安全考虑，前者应设为no，后者应设为no。
   - `PubkeyAuthentication`和`AuthorizedKeysFile`用于配置公钥认证的相关参数，这是一种更安全的认证方式。
3. **连接控制**：
   - `MaxAuthTries`设置了在进行身份验证时允许的最大尝试次数，这有助于防止暴力破解攻击。
   - `ClientAliveInterval`和`ClientAliveCountMax`用于设置客户端活动的检查频率和最大断开次数，有助于自动终止不活跃的连接。
4. **性能优化**：
   - `UseDNS`若设置为no，则禁止SSHD进行反向DNS查找，这可以减少登录延迟。
   - `GSSAPIOptions`可以调整GSSAPI（一种用于身份验证的机制）的行为，提高某些网络环境下的性能。
5. **日志记录**：
   - `SyslogFacility`和`LogLevel`分别控制了SSHD日志的消息级别和输出位置，这对调试和监控服务状态非常重要。

综上所述，可以看到SSHD及其配置文件提供了丰富的工具来确保远程访问的安全性和灵活性。正确理解和配置这些设置将极大地增强您的系统安全和网络效率。

## [wget命令 – 下载网络文件](https://www.linuxcool.com/wget)

> `wget` 是一个在 Linux 系统中常用的命令行工具，用于从网络上下载文件。它支持 HTTP、HTTPS 和 FTP 协议，并且可以通过多种方式进行下载，包括断点续传、批量下载等。下面是 `wget` 命令的常用参数及其详细解释：

**基本用法**

```shell
wget [参数] [URL]
```

**常用参数**

```shell
# -O [文件名] 将下载的文件保存为指定的文件名。
wget -O example.html https://example.com

# -P [目录] 将下载的文件保存到指定目录中。
wget -P /path/to/directory https://example.com/file.zip

# -c 继续下载已经开始但未完成的文件
wget -c https://example.com/largefile.zip

# -b 后台下载文件
wget -b https://example.com/largefile.zip
```

## [curl命令 – 文件传输工具](https://www.linuxcool.com/curl)
> `curl` 是一个用于在命令行中进行网络请求的工具，支持多种协议，如 HTTP、HTTPS、FTP 等。它非常强大，可以用来下载文件、提交表单、发送请求等。以下是 `curl` 常用的用法与参数的详细解释：

**基本用法**

```shell
curl [options...] <url>
```

**常用参数**

```shell
# -o, --output <file>    将输出保存到指定的文件中。
curl -o example.html http://www.example.com/

# -O, --remote-name     使用 URL 中的文件名将文件保存到本地。
curl -O http://www.example.com/index.html

# -L, --location   如果服务器返回 3XX 重定向，自动跟随重定向。
curl -L http://www.example.com/

# -C, --continue-at <offset>   断点续传，从指定的位置继续下载
curl -C - -O http://www.example.com/largefile.zip

# -u, --user user:password   使用指定的用户名和密码进行身份验证。
curl -u username:password http://www.example.com/

# -d, --data <data>   发送 POST 请求的数据，可以指定 URL 编码的数据
curl -d "param1=value1&param2=value2" http://www.example.com/form

#  -H, --header <header>    添加请求头
curl -H "Content-Type: application/json" http://www.example.com/

#  -X, --request <command>    指定请求方法，如 GET、POST、PUT、DELETE 等。
curl -X DELETE http://www.example.com/resource/1

# -I, --head  获取响应头信息，而不获取响应体。
curl -I http://www.example.com/
 
# -k, --insecure    允许不安全的 SSL 连接（如忽略 SSL 证书验证）
curl -k https://self-signed.badssl.com/

# -F, --form <name=content>  提交表单数据，支持文件上传。
curl -F "file=@/path/to/file" http://www.example.com/upload

# -s, --silent  静默模式，不输出任何信息（错误信息除外）。
curl -s http://www.example.com/

# -w, --write-out <format> 输出详细信息，例如响应时间等。
curl -w "Time: %{time_total}\n" -o /dev/null -s http://www.example.com/

# -x, --proxy [protocol://]host[]   使用代理服务器。
curl -x http://proxy.example.com:8080 http://www.example.com/

# --limit-rate <speed>  限制传输速度。
curl --limit-rate 100K http://www.example.com/largefile.zip
```

#  疑问知识点

## linux 命令中参数因该在什么位置使用

在 Linux 命令中，参数的位置有一定的规则。通常，Linux 命令的格式是：

```shell
command [options] [arguments]
```

> **命令 (command)**：这是执行的操作，如 `ls`, `cp`, `rm` 等。
>
> **选项 (options)**：选项通常以一个或两个破折号（`-` 或 `--`）开头，用于修改命令的行为。单破折号后的选项一般是单字符，例如 `-l`，双破折号后的选项是全称，例如 `--long`。选项可以放在命令之后的任意位置，但通常是紧跟在命令之后。
>
> **参数 (arguments)**：参数是命令作用的对象，比如文件名或目录名。参数通常放在所有选项之后。

例如：

```shell
ls -l /home/user
```

在这个命令中：

- `ls` 是命令，
- `-l` 是选项，
- `/home/user` 是参数。

再比如：

```sh
cp -r source_directory destination_directory
```

在这个命令中：

- `cp` 是命令，
- `-r` 是选项，
- `source_directory` 和 `destination_directory` 是参数。

有些命令的选项和参数可以混合使用，但最好保持选项在前，参数在后的规则，这样更易于阅读和避免错误。例如：

```sh
tar -cvf archive.tar file1 file2
```

在这个命令中：

- `tar` 是命令，
- `-cvf` 是选项（组合选项），
- `archive.tar` 和 `file1 file2` 是参数。

