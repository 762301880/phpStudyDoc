# 一、资料

资料

| 名称             | 地址                                                         |
| ---------------- | ------------------------------------------------------------ |
| 个人网盘提供下载 | [链接](https://yaoliuyang.lanzoui.com/iZh0Kqm6g7c)           |
| microsoft官网    | [链接](https://www.microsoft.com/zh-cn/software-download/windows10) |
| msdn系统下载     | [链接](https://msdn.itellyou.cn/)                            |
| 微PE官网         | [链接](http://www.wepe.com.cn/download.html?tdsourcetag=s_pcqq_aiomsg) |
| NEXTITELLyou     | [链接](https://next.itellyou.cn/)                            |

# 安装

## window7 [系统安装](https://www.sohu.com/a/290204822_663860)

### 下载镜像

> 首先去**[next.itellyou](https://next.itellyou.cn/Original/Index)**下载原版windows7镜像
>
> 这里下载64位版本复制连接地址那个都行
>
> 然后通过，[aria2](http://aria2.baisheng999.com/#Windows) 下载镜像

![1635553910(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/1haLs7ZVQSmJ6On.png)

###  制作U盘启动盘

#### [微PE工具下载](https://www.wepe.com.cn/download.html?tdsourcetag=s_pcqq_aiomsg)

**下载完成之后双击打开**-点击安装pe到U盘
![1635554683(1).png](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/FEd6nDJPVkvrQcC.png)

**选择U盘**

> 安装方法选择推荐即可
>
> 待写入U盘选择自己的U盘，一般情况下会默认选择
>
> 格式化选择默认即可，最后点击立即安装进U盘

![1635554975(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/XeDIbGFUuSRm7YE.png)

**转移下载的镜像到U盘启动盘中**

> 已经下载的U盘请复制到U盘中

按下图顺序选择“安装分区”和“系统镜像文件”（GHO系统镜像需提前下载好并放置于非系统盘）

![img](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/7cebaefded5d457cb3f9035726bfa328.png)

前一步“执行”后，在弹出窗口中勾选“重启”再确定

![img](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/5fc6ce49012e428c85da9d6fa709c040.png)

**装机完成后遇到问题**

无法识别U盘，[纯净系统默认没有网卡驱动](https://jingyan.baidu.com/article/17bd8e522b908785ab2bb89d.html),[驱动精灵](http://2t6y.mydown.com/tianji/f20.shtml?sfrom=166&keyID=16535),[驱动人生](https://www.160.com/),[下载之家](http://www.downza.cn/tags/win7%E7%BD%91%E5%8D%A1%E9%A9%B1%E5%8A%A8/)



#### [ventoy 多系统启动盘-推荐](https://www.ventoy.net/cn/)

> 好处就是这个启动盘**可以一个U盘安装多个系统镜像 可以是linux 也可以是windows**

**资料**

| 名称        | 地址                                                         |
| ----------- | ------------------------------------------------------------ |
| ventoy 官网 | [link](https://www.ventoy.net/cn/)                           |
| 博客参考    | [link](https://blog.csdn.net/weixin_45311714/article/details/126809854?utm_medium=distribute.pc_relevant.none-task-blog-2~default~baidujs_baidulandingword~default-0-126809854-blog-132497661.235^v39^pc_relevant_3m_sort_dl_base2&spm=1001.2101.3001.4242.1&utm_relevant_index=3) |

**下载ventoy**

![image-20231214103947814](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231214103947814.png)

##  补充:U盘格式区别

U 盘的格式决定了它能兼容的设备、支持的文件大小以及功能特性，不同格式各有适用场景。以下是常见 U 盘格式的详细区别：

### **一、主流 U 盘格式及核心差异**

| 格式      | 文件系统类型 | 最大单文件大小 | 最大分区容量 | 兼容性                                     | 主要特点                                                     |
| --------- | ------------ | -------------- | ------------ | ------------------------------------------ | ------------------------------------------------------------ |
| **FAT32** | 传统文件系统 | 4GB            | 2TB          | 极强（Windows、macOS、Linux、智能设备等）  | 兼容性最广，但单文件不能超过 4GB，不支持权限管理。           |
| **NTFS**  | Windows 原生 | 16EB           | 256TB        | 主要支持 Windows，macOS 仅读（需开启功能） | 支持大文件、权限管理、压缩、加密，适合 Windows 系统内使用。  |
| **exFAT** | 微软优化格式 | 16EB           | 256TB        | 较好（Windows、macOS、部分 Linux）         | 专为移动设备设计，兼顾大文件支持和跨平台兼容性，无 4GB 单文件限制。 |
| **APFS**  | macOS 原生   | 无实际限制     | 无实际限制   | 仅支持 macOS 10.13+                        | 苹果最新文件系统，支持快照、加密、快速复制，适合苹果设备专用 U 盘。 |
| **ext4**  | Linux 主流   | 16TB           | 1EB          | 主要支持 Linux，Windows/macOS 需工具       | Linux 系统默认格式，支持日志功能、大文件，适合 Linux 设备专用。 |

### **二、各格式深度解析**

#### 1. **FAT32**

- **优势**：
  几乎所有设备（电脑、电视、相机、车载系统、游戏机等）都能识别，是 “万能兼容” 的选择。
- 劣势
  - 单文件不能超过 4GB（如高清电影、大型游戏安装包无法存储）；
  - 分区容量最大 2TB，不支持文件权限和加密。
- **适用场景**：
  需要在多种设备间交换小文件（如文档、照片、音乐），或老旧设备（如 PS3、车载系统）。

#### 2. **NTFS**

- 优势
  - 支持超过 4GB 的大文件，分区容量上限极高；
  - 具备文件权限管理、压缩、加密（EFS）、日志功能（减少数据丢失风险）。
- 劣势
  - macOS 默认只能读取，写入需借助第三方工具（如 Paragon NTFS）；
  - 在非 Windows 设备上可能出现兼容性问题（如部分智能电视无法识别）。
- **适用场景**：
  仅在 Windows 设备间使用，或需要存储大文件（如视频剪辑、备份文件）。

#### 3. **exFAT**

- 优势
  - 结合了 FAT32 的兼容性和 NTFS 的大文件支持，单文件和分区容量无实用限制；
  - 比 NTFS 更轻便，适合移动存储（U 盘、SD 卡）。
- 劣势
  - 部分老旧设备（如 PS3、旧款相机）不支持；
  - 无日志功能，突然断电可能导致数据损坏。
- **适用场景**：
  需要在 Windows 和 macOS 间交换大文件（如 4K 视频、工程文件），或现代设备（如 PS4、Xbox）。

#### 4. **APFS（Apple File System）**

- **优势**：
  苹果设备专用优化，支持快速复制、快照（时间机器备份）、空间共享（多个分区共享容量）、原生加密。
- **劣势**：
  非苹果设备几乎无法识别，Windows/macOS 旧版本（<10.13）不支持。
- **适用场景**：
  仅用于苹果设备（如 Mac、iPhone 备份），或需要高效管理苹果生态文件。

#### 5. **ext4**

- **优势**：
  Linux 系统的 “原生格式”，支持日志、大文件、多用户权限，稳定性强。
- **劣势**：
  Windows 和 macOS 需安装额外软件（如 Ext2Fsd）才能读写，兼容性差。
- **适用场景**：
  Linux 服务器备份、嵌入式设备（如树莓派），或仅在 Linux 系统间使用。

### **三、如何选择 U 盘格式？**

1. **优先看设备兼容性**：
   - 多设备通用 → **FAT32**（小文件）或**exFAT**（大文件）；
   - 仅 Windows → **NTFS**；
   - 仅苹果 → **APFS**；
   - 仅 Linux → **ext4**。
2. **看文件大小**：
   - 有超过 4GB 的文件 → 排除 FAT32，选 exFAT/NTFS/APFS/ext4；
   - 都是小文件 → 优先 FAT32（兼容性）。
3. **特殊需求**：
   - 需要加密 / 权限管理 → NTFS（Windows）或 APFS（macOS）；
   - 老旧设备（如 PS3、车载） → 必须 FAT32。

### **四、格式转换注意事项**

- 转换格式会**清空 U 盘数据**，操作前务必备份；
- Windows 可通过 “格式化” 功能直接转换为 FAT32/NTFS/exFAT；
- macOS 需用 “磁盘工具”，可格式化为 exFAT/APFS（FAT32 需手动输入命令）；
- Linux 可通过 GParted 工具转换为 ext4/exFAT 等。



#  分区管理软件

## [DiskGenius](https://www.diskgenius.cn/)

> 各大U盘启动盘官方推荐分区软件

![image-20231007153345621](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20231007153345621.png)

# 远程开关机

## [开机卡](https://mp.weixin.qq.com/s/ZAul1X3WXb-PbdT836ONDQ)

# 系统分区大小设置

> 一下是我第一个笔记本的截图**实际体验来说 C盘100个G根本不够用**

![image-20250802091714188](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250802091714188.png)

| **盘符** | **分区大小** |
| -------- | ------------ |
| **C:**   | **100GB**    |
| **D:**   | **327GB**    |
| **E:**   | **244GB**    |
| **F:**   | **244GB**    |