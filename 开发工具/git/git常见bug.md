## Git问题：解决“ssh:connect to host github.com port 22: Connection timed out”

**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_45637036/article/details/106560217) |

## github登录解决两步验证

**参考资料**

| 名称                   | 地址                                                         |
| ---------------------- | ------------------------------------------------------------ |
| 网络博客               | [link](https://blog.csdn.net/qq_42611074/article/details/133377164?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-133377164-blog-103712414.235^v38^pc_relevant_anti_t3_base&spm=1001.2101.3001.4242.1&utm_relevant_index=1) |
| 谷歌身份验证器下载地址 | [谷歌商店](https://chromewebstore.google.com/detail/%E8%BA%AB%E4%BB%BD%E9%AA%8C%E8%AF%81%E5%99%A8/bhghoamapcdpbohphigoooaddinpkbai)  [官网](https://authenticator.cc/) |

**安装身份验证器**

![image-20231013142729857](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231013142729857.png)

## 切换分支报错 error: invalid path 'db::name'

> 解决参考          https://blog.csdn.net/Changxing_J/article/details/133910042

**问题原因**

> 代码中包含 NTFS 文件系统不支持的文件名。（源代码可能是在 Mac 或 Linux 等其他系统下开发的）
>
> Git 在 Windows 下默认开启了 NTFS 保护机制，导致包含不满足 NTFS 文件名的项目无法被成功拉取，且无法切换到这些不满足 NTFS 文件名规范的文件夹中。

**解决**

> 关闭 NTFS 保护机制的配置，操作命令如下：

```shell
git config core.protectNTFS false
```

