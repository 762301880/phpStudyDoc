# 资料

| 名称           | 地址                                                         |
| -------------- | ------------------------------------------------------------ |
| 个人坚果云笔记 | [link](https://www.jianguoyun.com/p/DTmdTCsQq7neChjmpckEIAA) |
| 菜鸟教程       | [Shell 教程](https://www.runoob.com/linux/linux-shell.html)  |

## 示例

**编写规范**

> `#!/bin/bash` 的意思是指定告知系统当前这个脚本要使用的**shell**解释器

```shell
#!/bin/bash

# 文件命名规范：

文件名.sh .sh是linux下bash shell 的默认后缀
```

[**重点补充(反引号的作用)**](https://wenku.baidu.com/view/2b2a85d3f51fb7360b4c2e3f5727a5e9846a2745.html	)

> ``反引号，命令替换，通常把命令输出结果保存在变量中

```shell
[root@VM-16-5-centos ~]# cat test.sh 
#!/bin/bash

dt=`date +'%F %T'`

echo $dt
[root@VM-16-5-centos ~]# ./test.sh 
2022-07-05 22:10:20
```

## [文件测试运算符](https://www.runoob.com/linux/linux-shell-basic-operators.html)

文件测试运算符用于检测 Unix 文件的各种属性。

属性检测描述如下：

| 操作符  | 说明                                                         | 举例                      |
| :------ | :----------------------------------------------------------- | :------------------------ |
| -b file | 检测文件是否是块设备文件，如果是，则返回 true。              | [ -b $file ] 返回 false。 |
| -c file | 检测文件是否是字符设备文件，如果是，则返回 true。            | [ -c $file ] 返回 false。 |
| -d file | 检测文件是否是目录，如果是，则返回 true。                    | [ -d $file ] 返回 false。 |
| -f file | 检测文件是否是普通文件（既不是目录，也不是设备文件），如果是，则返回 true。 | [ -f $file ] 返回 true。  |
| -g file | 检测文件是否设置了 SGID 位，如果是，则返回 true。            | [ -g $file ] 返回 false。 |
| -k file | 检测文件是否设置了粘着位(Sticky Bit)，如果是，则返回 true。  | [ -k $file ] 返回 false。 |
| -p file | 检测文件是否是有名管道，如果是，则返回 true。                | [ -p $file ] 返回 false。 |
| -u file | 检测文件是否设置了 SUID 位，如果是，则返回 true。            | [ -u $file ] 返回 false。 |
| -r file | 检测文件是否可读，如果是，则返回 true。                      | [ -r $file ] 返回 true。  |
| -w file | 检测文件是否可写，如果是，则返回 true。                      | [ -w $file ] 返回 true。  |
| -x file | 检测文件是否可执行，如果是，则返回 true。                    | [ -x $file ] 返回 true。  |
| -s file | 检测文件是否为空（文件大小是否大于0），不为空返回 true。     | [ -s $file ] 返回 true。  |
| -e file | 检测文件（包括目录）是否存在，如果是，则返回 true。          | [ -e $file ] 返回 true。  |

### 案例

```shell
# 先创建所需文件或目录
touch a.txt && mkdir b

# 编写shell脚本
vim test.sh
```

**脚本内容**

```shell
 #!/bin/bash
# 定义文件的路径
filepath='/root/a.txt'
if [ -f $filepath ]
   then 
     echo “我是文件夹路径 $filepath”
   else
     echo "$filepath 不是一个文件夹路径"
fi
# 定义文件的路径
dirpath='/root/b'

if [ -d $dirpath ] 
   then
     echo "$dirpath 是一个目录"
   else
     echo "$dirpath 不是一个目录"
fi
```

**输出**

```shell
[root@VM-16-5-centos ~]# ./test.sh 
“我是文件夹路径 /root/a.txt”
/root/b 是一个目录
```





##  脚本实践

### 禁用<font color='red'>rm -rf /* </font>命令

**添加脚本 disable_rm.sh **

```shell
cd /usr/local/src && touch disable_rm.sh && chmod +x disable_rm.sh
```

**添加脚本内容**

```shell
#!/bin/bash

disable_rm_command='rm -rf /*'
parameter_one='-rf' # 比较命令1
parameter_two='/bin' # 比较命令2

# 注意/* 通配符 传递过来值为 /bin
if [ "$1"="$parameter_one" ] &&  [  "$2"  = "$parameter_two"  ] 
   then
   echo "对不起非法命令,严禁使用 $disable_rm_command 命令"
   else
fi  
```

**添加别名**

```shell
# 编辑别名
[root@VM-16-5-centos ~]# cd ~ && vim .bashrc


#alias rm='rm -i'     # 注释之前的rm命令
alias cp='cp -i'
alias mv='mv -i'
alias rm='/usr/local/src/disable_rm.sh'     # 重新添加rm别名指向脚本
```

**结果示例**

> <font color='red'>重新进入终端输入命令</font>

```shell
[root@VM-16-5-centos ~]# rm -rf /*
对不起非法命令,严禁使用 rm -rf /* 命令
# 创建 a.txt 文件 && b目录
[root@VM-16-5-centos ~]# touch a.txt && mkdir b && ls
a.txt  b
# 删除文件&目录并查看
[root@VM-16-5-centos ~]# rm -f a.txt  && rm -rf b && ls
[root@VM-16-5-centos ~]# 
```

# 注意事项

### [定义变量之间左右不要有<font color='red'>空格</font>](https://blog.csdn.net/xbnlkdbxl/article/details/52945608?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_title~default-1-52945608-blog-89491253.pc_relevant_aa&spm=1001.2101.3001.4242.2&utm_relevant_index=4)

**例子**

> 其实在定义shell脚本的变量时在变量名和等号之间是不允许有空格的，而我写的是you_name和=之间有空格，所以系统默认为，you_name是一个命令，所以系统找不到这个命令

```shell
# 定义a.sh
[root@VM-16-5-centos src]# vim a.sh 

#!/bin/bash

you_name= “zhangsan”
echo $you_name
# 设置可执行权限
[root@VM-16-5-centos src]# chmod +x a.sh
# 输出示例
[root@VM-16-5-centos src]# ./a.sh 
./a.sh: line 3: $'\342\200\234zhangsan\342\200\235': command not found

#-------------------------修改后------------------------
# 删除等于号之间的空格
[root@VM-16-5-centos src]# cat a.sh 
#!/bin/bash
you_name=“zhangsan”
echo $you_name

# 输出示例
[root@VM-16-5-centos src]# ./a.sh 
“zhangsan”
```

# 知识点

##  shell中启动多个任务

> 在Linux shell中，`wait`命令用于等待子进程的完成。当你在shell脚本中启动一个后台进程（或者使用`&`运算符），可以使用`wait`命令来暂停当前进程，直到所有子进程完成执行。
>
> 当使用`wait`命令时，它会阻塞当前进程，直到所有子进程退出。这可以用于确保所有子任务都已完成，然后再继续执行后续的操作。
>
> 下面是一个使用`wait`命令的简单示例：

```shell
#!/bin/bash

# 启动后台进程
command1 &
command2 &
command3 &

# 等待所有子进程完成
wait

echo "所有子任务已完成"
```

> 在这个例子中，`command1`、`command2`和`command3`是三个并行执行的子进程。`wait`命令会等待这些子进程全部完成后，才会继续执行脚本后面的语句。这样可以确保在执行`echo`语句之前，所有的子任务都已经完成了。

## 实现-利用shell脚本实现秒级定时任务

> https://www.yisu.com/zixun/486965.html
> 实现-利用shell脚本实现秒级定时任务
> 利用shell脚本实现
> 编写shell脚本-seconds.sh

```shell
#!/bin/bash
step=1 #间隔的秒数

for (( i = 0; i < 60; i=(i+step) )); do
   cd ~ && echo  $(date "+%Y-%m-%d %X") >> a.txt  # 需要执行的定时脚本-这里为了模拟写入文件执行时间
 sleep $step # 随眠定义的秒
done

exit 0
```

# shell脚本加密

> https://zhuanlan.zhihu.com/p/1899872786790462703

## 使用 [gzexe](https://www.runoob.com/linux/linux-comm-gzexe.html) 来进行加密

```shell
# 加密
gzexe myshell.sh
# 执行文件
myshell
# 解密
gzexe -d myshell.sh
```

## 使用 shc 来进行加密