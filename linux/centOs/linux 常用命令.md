	[^author:姚留洋]

[^created_at:2021/10/24]

### 输出重定向

> 一般命令的输出都会显示在终端中,有时候需要将一些命令的执行结果保存到文件中
>
> 进行后寻的分析/统计，则这时候需要使用到的输出重定向技术。

- 命令

```shell
> # 覆盖输输出（一个大于号），会覆盖原有的文件内容
>> # 追加输出（两个个大于号），不会不该原始文件内容，会在原始内容末尾继续添加
# 语法 正常执行的指令 >/>> 文件的路径
# 注意 ：文件可以不存在,不存在则新建
# 案例：使用覆盖重定向,保存ls -la 的执行结果,保存到当前目录下的ls.txt
```

- 案例

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

### 管道命令 

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

- 过滤例子

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

### 操作服务器的主机名

> `hostname`   读取设置主机名

```shell
# 查看当前的主机名 含义:表示输出完整的主机名 
$ hostname 
yaoliuyang-PC
# 含义:表示输出当前主机名中的FQDN(全限定域名)
$ hostname -f
yaoliuyang-PC
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

## vim命令

**资料**

| 名称         | 地址                                                |
| ------------ | --------------------------------------------------- |
| 菜鸟教程-vim | [link](https://www.runoob.com/linux/linux-vim.html) |

**常用命令示例**

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
:u  # 命令行输入字母u  Enter键确定(重复撤销 重复操作)
u # 命令模式 直接u  重复撤销重复操作
# 2. 恢复
ctrl + r # 恢复(取消)之前的撤销

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
: n          # 末行模式下(: 冒号输入命令的模式下) 冒号输入字母上排的数字  Enter键q
```















