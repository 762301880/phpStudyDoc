**资料**

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 网络博客   | [link](https://blog.csdn.net/shamqu/article/details/123288539) |
| rclone官网 | [link](https://rclone.org/)                                  |

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



**设置**

```shell
root@yaoliuyang:/# rclone config 
No remotes found, make a new one?
n) New remote
s) Set configuration password
q) Quit config
n/s/q> n

Enter name for new remote.
name> aliyun

Option Storage.
Type of storage to configure.
Choose a number from below, or type in your own value.
 1 / 1Fichier
   \ (fichier)
 2 / Akamai NetStorage
   \ (netstorage)
 3 / Alias for an existing remote
   \ (alias)
 4 / Amazon Drive
   \ (amazon cloud drive)
 5 / Amazon S3 Compliant Storage Providers including AWS, Alibaba, ArvanCloud, Ceph, ChinaMobile, Cloudflare, DigitalOcean, Dreamhost, GCS, HuaweiOBS, IBMCOS, IDrive, IONOS, LyveCloud, Leviia, Liara, Linode, Minio, Netease, Petabox, RackCorp, Rclone, Scaleway, SeaweedFS, StackPath, Storj, Synology, TencentCOS, Wasabi, Qiniu and others
   \ (s3)
 6 / Backblaze B2
   \ (b2)
 7 / Better checksums for other remotes
   \ (hasher)
 8 / Box
   \ (box)
 9 / Cache a remote
   \ (cache)
10 / Citrix Sharefile
   \ (sharefile)
11 / Combine several remotes into one
   \ (combine)
12 / Compress a remote
   \ (compress)
13 / Dropbox
   \ (dropbox)
14 / Encrypt/Decrypt a remote
   \ (crypt)
15 / Enterprise File Fabric
   \ (filefabric)
16 / FTP
   \ (ftp)
17 / Google Cloud Storage (this is not Google Drive)
   \ (google cloud storage)
18 / Google Drive
   \ (drive)
19 / Google Photos
   \ (google photos)
20 / HTTP
   \ (http)
21 / Hadoop distributed file system
   \ (hdfs)
22 / HiDrive
   \ (hidrive)
23 / ImageKit.io
   \ (imagekit)
24 / In memory object storage system.
   \ (memory)
25 / Internet Archive
   \ (internetarchive)
26 / Jottacloud
   \ (jottacloud)
27 / Koofr, Digi Storage and other Koofr-compatible storage providers
   \ (koofr)
28 / Linkbox
   \ (linkbox)
29 / Local Disk
   \ (local)
30 / Mail.ru Cloud
   \ (mailru)
31 / Mega
   \ (mega)
32 / Microsoft Azure Blob Storage
   \ (azureblob)
33 / Microsoft Azure Files
   \ (azurefiles)
34 / Microsoft OneDrive
   \ (onedrive)
35 / OpenDrive
   \ (opendrive)
36 / OpenStack Swift (Rackspace Cloud Files, Blomp Cloud Storage, Memset Memstore, OVH)
   \ (swift)
37 / Oracle Cloud Infrastructure Object Storage
   \ (oracleobjectstorage)
38 / Pcloud
   \ (pcloud)
39 / PikPak
   \ (pikpak)
40 / Proton Drive
   \ (protondrive)
41 / Put.io
   \ (putio)
42 / QingCloud Object Storage
   \ (qingstor)
43 / Quatrix by Maytech
   \ (quatrix)
44 / SMB / CIFS
   \ (smb)
45 / SSH/SFTP
   \ (sftp)
46 / Sia Decentralized Cloud
   \ (sia)
47 / Storj Decentralized Cloud Storage
   \ (storj)
48 / Sugarsync
   \ (sugarsync)
49 / Transparently chunk/split large files
   \ (chunker)
50 / Union merges the contents of several upstream fs
   \ (union)
51 / Uptobox
   \ (uptobox)
52 / WebDAV
   \ (webdav)
53 / Yandex Disk
   \ (yandex)
54 / Zoho
   \ (zoho)
55 / premiumize.me
   \ (premiumizeme)
56 / seafile
   \ (seafile)


Storage> 52

Option url.
URL of http host to connect to.
E.g. https://example.com.
Enter a value.
url> http://60.204.148.255:5244/dav

Option vendor.
Name of the WebDAV site/service/software you are using.
Choose a number from below, or type in your own value.
Press Enter to leave empty.
 1 / Fastmail Files
   \ (fastmail)
 2 / Nextcloud
   \ (nextcloud)
 3 / Owncloud
   \ (owncloud)
 4 / Sharepoint Online, authenticated by Microsoft account
   \ (sharepoint)
 5 / Sharepoint with NTLM authentication, usually self-hosted or on-premises
   \ (sharepoint-ntlm)
 6 / rclone WebDAV server to serve a remote over HTTP via the WebDAV protocol
   \ (rclone)
 7 / Other site/service or software
   \ (other)
vendor> 7

Option user.
User name.
In case NTLM authentication is used, the username should be in the format 'Domain\User'.
Enter a value. Press Enter to leave empty.

user> admin

Option pass.
Password.
Choose an alternative below. Press Enter for the default (n).
y) Yes, type in my own password
g) Generate random password
n) No, leave this optional password blank (default)
y/g/n> y
Enter the password:
password:
Confirm the password:
password:

Option bearer_token.
Bearer token instead of user/pass (e.g. a Macaroon).
Enter a value. Press Enter to leave empty.
# 设置token
bearer_token>        # 直接回车

Edit advanced config?
y) Yes
n) No (default)
y/n> n       # 选n

Configuration complete.
Options:
- type: webdav
- url: http://60.204.148.255:5244/dav
- vendor: other
- user: admin
- pass: *** ENCRYPTED ***
- bearer_token: 
Keep this "aliyun" remote?
y) Yes this is OK (default)
e) Edit this remote
d) Delete this remote
y/e/d> y       # 没问题的话选y

Current remotes:

Name                 Type
====                 ====
aliyun               webdav

e) Edit existing remote
n) New remote
d) Delete remote
r) Rename remote
c) Copy remote
s) Set configuration password
q) Quit config
e/n/d/r/c/s/q> q        # q 退出


```

**查看rclone配置**

```shell
root@yaoliuyang:~# rclone config show
[aliyun]
type = webdav
url = http://60.204.148.255:5244/dav
vendor = other
user = admin
pass = e2hGCp4Oj1W0okIHaVPYMd5ObzHALPFIwjY
bearer_token = eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiJ9.eyJzdWIiOiJhYWEyYzdkZmZhMTk0NWZiYmFmZWZhZWUxNDdjOGQ0YyIsImF1ZCI6Ijc2OTE3Y2NjY2Q0NDQxYzM5NDU3YTA0ZjYwODRmYjJmIiwiZXhwIjoxNzEwNDg1Njc5LCJpYXQiOjE3MDI3MDk2NzksImp0aSI6IjE5ZTJmYTBlODg2MDQ5ZGI4Njk1MzgyNmZhYzkwZThkIn0.MWaNrMXQ-D9HibgoVHVcY5TwBPVUF-VBt3yZpC8IsIyZCSkASG3ENNbcm_d7fXBm55Tjs3ETBoqNKXftAp6TEg
```



**挂载**

> **/www/aliyun**目录需要自行创建
>
> 这个命令是用于挂载阿里云盘到本地的。具体解释如下：
>
> 1. `rclone mount`: 这是一个用于挂载云存储的命令。
> 2. `aliyun:/ /www/aliyun`: 这是要挂载的阿里云盘的路径，其中`aliyun`是你的阿里云盘名称，`/`是本地挂载的路径。
> 3. `--cache-dir /tmp`: 这个参数指定了缓存目录，用于加速文件传输。
> 4. `--allow-other`: 这个参数允许其他用户访问挂载的文件系统。
> 5. `--vfs-cache-mode writes`: 这个参数设置了虚拟文件系统（VFS）的缓存模式为写入模式，这意味着在写入文件时会将更改写入缓存，而不是直接写入磁盘。
> 6. `--allow-non-empty`: 这个参数允许挂载非空的文件夹。
> 7. `--uid 0 --gid 0`: 这两个参数分别设置了挂载点的所有者和组ID，这里设置为0表示使用root用户和组。

```shell
rclone mount aliyun:/ /www/aliyun --cache-dir /tmp --allow-other --vfs-cache-mode writes --allow-non-empty --uid 0 --gid 0


# 后台实现挂载

nohup  rclone mount aliyun:/ /www/aliyun --cache-dir /tmp --allow-other --vfs-cache-mode writes --allow-non-empty --uid 0 --gid 0   &
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

## 通过rclone挂载webdav为本地硬盘，阿里网盘的文件不能播放

> <font color='red'>通过实验目前阿里云盘不可以挂载   天翼云盘可以挂载视频播放</font>

> https://github.com/alist-org/alist/discussions/402

> <font color='red'>用alist的webdiv行不通的 要用aliyundrive-webdav</font>