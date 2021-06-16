#   资料

| 名称                                            | 地址                                                         |
| ----------------------------------------------- | ------------------------------------------------------------ |
| [crontab执行时间计算](https://tool.lu/crontab/) | [链接](https://tool.lu/crontab/)                             |
| 菜鸟教程                                        | [链接](https://www.runoob.com/w3cnote/linux-crontab-tasks.html) |

- 说明

> ## Crontab 是用来实现定时任务的linux工具我们经常需要使用Crontab 执行某一段脚本

# 二、使用

## 2.1 基本命令

```shell
crontab [-u username]　　　　//省略用户表表示操作当前用户的crontab
    -e      (编辑工作表)
    -l      (列出工作表里的命令)
    -r      (删除工作作)
```

## 2.2 实例

```php
0 6 * * * curl 您需要执行的地址 # 每天早上六点执行一个api
```

