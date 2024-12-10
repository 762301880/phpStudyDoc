# 资料

| 名称              | 地址                                                         |
| ----------------- | ------------------------------------------------------------ |
| adb工具官方安装包 | [link](https://developer.android.google.cn/tools/adb?hl=en)     [download](https://developer.android.google.cn/tools/releases/platform-tools?hl=en) |
| 网络博客          | [link](https://www.cnblogs.com/ximaomao/p/14081479.html)     |



# 环境变量配置

例如我的解压目录是`D:\Program Files\platform-tools`    直接把这个配置到 **系统变量  -   Path**  变量里即可

#  手机开启USB调试

vivo手机   **系统管理>开发者选项>USB调试**

#  使用

1. 安装 ADB 工具：

   - 如果你还没有安装 ADB，可以从 Android 开发者官网 下载 Android SDK Platform-Tools。

2. 连接设备：

   - 使用 USB 数据线将你的 Android 设备连接到电脑。

   - 在设备上启用开发者选项和 USB 调试。通常可以在 设置 > 关于手机 > 软件信息 中连续点击“版本号”7次来启用开发者选项，然后在 设置 > 系统 > 开发

     选项 中启用 USB 调试。

3. 打开命令行工具：

   - 打开终端（Windows 用户可以使用 CMD 或 PowerShell，Mac 和 Linux 用户可以使用 Terminal）。

4. 运行 ADB 命令：

   - 输入以下命令列出所有已安装的应用及其包名：

     ```shell
     adb shell pm list packages
     #  写入到磁盘
     adb shell pm list packages  > d:packagelist.txt
     ```

     如果你想查找特定应用的包名，可以使用 grep 命令进行过滤，例如：

     ```shell
          adb shell pm list packages | grep com.example
     ```

     

# 方法二：使用第三方应用

安装第三方应用：
在 Google Play 商店中搜索并安装一些专门用于查看应用包名的第三方应用，如 Package Name Viewer、App Inspector 等。
使用应用：
打开安装好的第三方应用，它会列出所有已安装的应用及其包名。你可以通过搜索或滚动列表找到你需要的应用包名。
方法三：使用设备自带的设置
进入应用信息页面：
打开设备的 设置 应用。
选择 应用 或 应用管理。
找到你想要查看包名的应用，点击进入其详细信息页面。
查看包名：
在应用的详细信息页面中，通常会有一个“应用信息”或“高级”部分，里面会显示应用的包名。具体位置可能会因设备型号和系统版本而有所不同。