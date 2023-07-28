#  使用 mydumper/myloader 将大型数据库迁移

## 资料

| 名称                                                         | 地址                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [使用 mydumper/myloader 将大型数据库迁移到 Azure Database for MySQL](https://learn.microsoft.com/zh-cn/azure/mysql/migrate/concepts-migrate-mydumper-myloader) | [link](https://learn.microsoft.com/zh-cn/azure/mysql/migrate/concepts-migrate-mydumper-myloader) |
| 官网                                                         | [MySQL Data Dumper in Launchpad](https://launchpad.net/mydumper) |
| github                                                       | [GitHub - mydumper/mydumper：官方MyDumper项目](https://github.com/mydumper/mydumper) |
| 其它博客参考                                                 | [link](https://docs.pingcap.com/zh/tidb/v4.0/backup-and-restore-using-mydumper-lightning) [link](https://www.codenong.com/cs106518061/) [link](https://www.cnblogs.com/keme/p/11679446.html) |

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

#### centos安装

在CentOS上编译和安装mydumper可以按照以下步骤进行操作：

1. 安装编译依赖性：
   在编译前，需要安装一些依赖包和开发工具。执行以下命令以安装这些依赖项：

```
sudo yum install -y cmake gcc glib2-devel mysql-devel zlib-devel
```

1. 下载mydumper源代码：
   通过以下命令下载mydumper源代码：

```
wget https://github.com/maxbube/mydumper/archive/refs/tags/v0.9.5.tar.gz
```

1. 解压和编译源代码：
   执行以下命令解压并编译源代码：

```
tar -zxvf v0.9.5.tar.gz
cd mydumper-0.9.5/
cmake .
make
```

1. 安装mydumper：
   成功编译后，执行以下命令将mydumper安装到系统中：

```
sudo make install
```

1. 验证安装：
   执行以下命令验证mydumper是否正确安装：

```
mydumper --help
```

如果命令成功执行，说明mydumper已经成功安装在你的系统中。

现在你可以使用mydumper备份和恢复MySQL数据库了。你可以按照上面提到的方式来执行备份和恢复操作。

希望以上信息能对你有所帮助！如有任何疑问，请随时提问。

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







[root@iZwz9fhv99le4f935sqr8rZ mydumper-0.9.5]# cmake .
cmake: symbol lookup error: cmake: undefined symbol: archive_write_add_filter_zstd



[root@iZwz9fhv99le4f935sqr8rZ mydumper-0.9.5]#  yum install  libarchive
Last metadata expiration check: 0:08:55 ago on Fri 28 Jul 2023 05:05:20 PM CST.
Package libarchive-3.3.2-8.el8_1.x86_64 is already installed.
Dependencies resolved.



https://blog.csdn.net/chenyulancn/article/details/118540210





```shell
# 导入示例
 myloader -u yao_test -p H5ZKdTcr7s2Hjsy7 -B yao_test -d /data/backup -o
```

