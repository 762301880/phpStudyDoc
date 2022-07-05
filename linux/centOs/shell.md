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

