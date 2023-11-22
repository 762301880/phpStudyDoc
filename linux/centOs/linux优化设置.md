## 设置系统默认语言为中文

**资料**

| 名称                    | 地址                                                        |
| ----------------------- | ----------------------------------------------------------- |
| 博客-系统语言设置为中文 | [link](https://cloud.tencent.com/developer/article/1640802) |



```shell
# 查看 系统默认语言
[root@VM-16-5-centos ~]# echo $LANG
en_US.UTF-8          # 此标志就是为英文

# 设置系统默认语言为中文
[root@VM-16-5-centos ~]# LANG=zh_CN.UTF-8
[root@VM-16-5-centos ~]# echo $LANG
zh_CN.UTF-8

# 测试
[root@VM-16-5-centos ~]# yum --help
........
check          检查 RPM 数据库问题
check-update   检查是否有可用的软件包更新
clean          删除缓存数据
deplist        列出软件包的依赖关系
........

# 将系统默认语言修改为英文
[root@VM-16-5-centos ~]# LANG=en_US.UTF-8
[root@VM-16-5-centos ~]# echo $LANG
en_US.UTF-8
```

