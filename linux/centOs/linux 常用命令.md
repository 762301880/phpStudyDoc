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
```



























