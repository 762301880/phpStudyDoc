#  使用 mydumper/myloader 将大型数据库迁移

## 资料

| 名称                                                         | 地址                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL](https://learn.microsoft.com/zh-cn/azure/mysql/migrate/concepts-migrate-mydumper-myloader) | [link](https://learn.microsoft.com/zh-cn/azure/mysql/migrate/concepts-migrate-mydumper-myloader) |
| 官网                                                         | [MySQL Data Dumper in Launchpad](https://launchpad.net/mydumper) |
| github                                                       | [GitHub - mydumper/mydumper：官方MyDumper项目](https://github.com/mydumper/mydumper) |
| 其它博客参考                                                 | [link](https://docs.pingcap.com/zh/tidb/v4.0/backup-and-restore-using-mydumper-lightning) [link](https://www.codenong.com/cs106518061/) |

## 安装 mydumper



```shell
wget https://github.com/mydumper/mydumper/archive/refs/tags/v0.15.1-1.tar.gz

tar -zxvf v0.15.1-1.tar.gz

cd mydumper-0.15.1-1      # 根据解压得到的目录名进行调整

cmake .
make && make install

# 验证MyDumper是否成功安装
mydumper --version   
```



# 卸载mydumper

要卸载mydumper，您可以按照以下步骤操作：

1. 打开终端或命令提示符窗口。

2. 如果您是使用包管理器安装的mydumper，例如在Ubuntu上使用apt或在CentOS上使用yum，可以使用相应的包管理器卸载mydumper。例如，使用apt卸载命令：

   ```
   sudo apt remove mydumper
   ```

   或者使用yum卸载命令：

   ```
   sudo yum remove mydumper
   ```

3. 如果您是手动安装的mydumper，可以通过以下步骤进行卸载：
   a. 导航到mydumper安装目录。默认情况下，mydumper安装在/usr/local/bin下。
   b. 删除mydumper的可执行文件：

   ```
   sudo rm /usr/local/bin/mydumper
   sudo rm /usr/local/bin/myloader
   ```

   c. 如果您在系统的其他位置放置了mydumper相关文件（例如配置文件或日志文件），也可以将其删除。

4. 完成上述步骤后，mydumper应该已成功卸载。

请注意，如果您的mydumper安装是通过其他方式进行的，那么卸载步骤可能会略有不同。在执行卸载操作之前，请确保备份重要数据，并仔细阅读相关文档或咨询社区以获取更准确的卸载指南。