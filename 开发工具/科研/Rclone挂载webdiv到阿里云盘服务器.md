**资料**

| 名称     | 地址                                                         |
| -------- | ------------------------------------------------------------ |
| 网络博客 | [link](https://blog.csdn.net/shamqu/article/details/123288539) |

**阿里云盘挂载服务器当本地盘**

**安装**

```shell
curl https://rclone.org/install.sh | sudo bash
```

**安装成功提示**

```shell
Processing manual pages under /usr/local/man...
mandb: can't update index cache /var/cache/man/oldlocal/1855092: No such file or directory
Updating index cache for path `/usr/local/man/man1'. Wait...mandb: warning: /usr/local/man/man1/rclone.1: whatis parse for rclone(1) failed
done.
Checking for stray cats under /usr/local/man...
Checking for stray cats under /var/cache/man/oldlocal...
2 man subdirectories contained newer manual pages.
2 manual pages were added.
0 stray cats were added.
0 old database entries were purged.
bash: line 200: errors: No such file or directory

 has successfully installed.
Now run "rclone config" for setup. Check https://rclone.org/docs/ for more details.
```



**挂载**

> **/www/aliyun**目录需要自行创建

```shell
rclone mount aliyun:/ /www/aliyun --cache-dir /tmp --allow-other --vfs-cache-mode writes --allow-non-empty --uid root --gid root
```

## bug解析

## 挂载报错 mount helper error: fusermount3: failed to access mountpoint

**参考资料**

| name     | url                                                         |
| -------- | ----------------------------------------------------------- |
| 网络博客 | [link](https://cloud.tencent.com/developer/article/2189948) |



```shell
root@yaoliuyang:/www/aliyun# rclone mount aliyun:/ /www/aliyun --cache-dir /tmp --allow-other --vfs-cache-mode writes --allow-non-empty 
2023/12/16 16:21:16 mount helper error: fusermount3: failed to access mountpoint /www/aliyun: Transport endpoint is not connected
2023/12/16 16:21:16 Fatal error: failed to mount FUSE fs: fusermount: exit status 1

```

**解决**

> 注意此时不能有任何多余的终端正在操作目录**/www/aliyun**

```shell
fusermount -u /www/aliyun
```

