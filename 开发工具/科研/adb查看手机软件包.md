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

#  案例

##  控制手机刷视频

```shell
#!/bin/bash

# 颜色定义
GREEN='\e[32m'
YELLOW='\e[33m'
CYAN='\e[36m'
RESET='\e[0m'

counter=0  # 初始化计数器

while true
do
  ((counter++))  # 计数器自增

  # 彩色输出当前次数
  echo -e "${GREEN}>>> 正在执行第 ${YELLOW}$counter ${GREEN}遍滑动 ${RESET}"

  # 执行滑动操作
  adb shell input swipe 500 1500 500 500 300

  # 生成随机延迟
  delay=$(( ( RANDOM % 4 ) + 5 ))

  echo -ne "${CYAN}🕒 倒计时： ${RESET}"

  # 动态倒计时
  for ((i=delay; i>0; i--)); do
    echo -ne "${CYAN}${i} 秒...${RESET} \r"
    sleep 1
  done

  echo ""  # 换行
done
```

# ADB命令速查表

以下是常用 **ADB（Android Debug Bridge）命令大全**，涵盖了设备连接、应用管理、文件操作、日志调试等多个方面，适合开发、测试及调试使用：

------

### 🔌 一、设备连接与管理



| 命令                                   | 说明                           |
| -------------------------------------- | ------------------------------ |
| `adb devices`                          | 查看已连接设备列表             |
| `adb connect <ip>:<port>`              | 连接远程设备                   |
| `adb disconnect`                       | 断开所有远程连接               |
| `adb kill-server` / `adb start-server` | 重启 ADB 服务                  |
| `adb get-state`                        | 获取设备状态（device/offline） |
| `adb reboot`                           | 重启设备                       |
| `adb reboot bootloader`                | 重启到 bootloader 模式         |
| `adb reboot recovery`                  | 重启到 recovery 模式           |

------

### 📱 二、安装与卸载应用



| 命令                         | 说明                     |
| ---------------------------- | ------------------------ |
| `adb install <apk路径>`      | 安装 APK                 |
| `adb install -r <apk路径>`   | 重新安装（保留数据）     |
| `adb uninstall <包名>`       | 卸载应用                 |
| `adb uninstall -k <包名>`    | 卸载应用但保留数据和缓存 |
| `adb shell pm list packages` | 列出所有包名             |

------

### 🧭 三、应用控制与启动



| 命令                                                         | 说明              |
| ------------------------------------------------------------ | ----------------- |
| `adb shell monkey -p <包名> -c android.intent.category.LAUNCHER 1` | 启动应用          |
| `adb shell am start -n <包名>/<Activity名>`                  | 精确启动 Activity |
| `adb shell am force-stop <包名>`                             | 强制停止应用      |
| `adb shell pm clear <包名>`                                  | 清除应用数据      |

------

### 📂 四、文件传输与管理



| 命令                             | 说明                   |
| -------------------------------- | ---------------------- |
| `adb push <本地文件> <设备路径>` | 将文件推送到设备       |
| `adb pull <设备文件> <本地路径>` | 将文件从设备拉取到本地 |
| `adb shell ls <路径>`            | 查看设备上的文件       |
| `adb shell rm <路径>`            | 删除设备上的文件       |

------

### 🐚 五、Shell 操作



| 命令                                      | 说明             |
| ----------------------------------------- | ---------------- |
| `adb shell`                               | 进入设备 Shell   |
| `adb shell <命令>`                        | 在设备上执行命令 |
| `adb shell dumpsys`                       | 查看系统服务信息 |
| `adb shell screencap /sdcard/screen.png`  | 截图             |
| `adb shell screenrecord /sdcard/demo.mp4` | 录屏             |
| `adb shell input text "hello"`            | 模拟输入         |
| `adb shell input tap x y`                 | 模拟点击         |
| `adb shell input swipe x1 y1 x2 y2`       | 模拟滑动         |

------

### 🧪 六、调试与日志



| 命令                   | 说明              |
| ---------------------- | ----------------- |
| `adb logcat`           | 查看设备日志      |
| `adb logcat -s <TAG>`  | 过滤指定 TAG 日志 |
| `adb logcat > log.txt` | 将日志保存到文件  |
| `adb bugreport`        | 获取完整 bug 报告 |

------

### 🧰 七、网络与端口转发



| 命令                                | 说明                       |
| ----------------------------------- | -------------------------- |
| `adb forward <本地端口> <远程端口>` | 设置端口转发               |
| `adb reverse <远程端口> <本地端口>` | 反向端口转发（适用于开发） |

## 常用命令

```shell
# 获取屏幕分辨率（宽x高）  例如输出Physical size: 1080x2340，表示屏幕宽度 1080 像素，高度 2340 像素。
adb shell wm size

# 查看已连接设备列表
adb devices
```



#  补充

##  无线连接手机adb

> 环境:**手机连接公司wifi**  **电脑连接的是公司网线**

**手机端设置**

> 打开 **开发者选项 → 无线调试**。
>
> **然后点击无线调试 会弹出配对信息界面**



### **cmd命令配置**

[**使用配对码配对设备**](https://blog.csdn.net/huaqianzkh/article/details/143199399)

> 使用配对码
>
> 在手机上生成配对码。
>
> 打开电脑终端，输入以下命令：

```shell
# 命令
adb pair <手机IP地址>:<端口号>


# 命令示例
C:\Users\铺先生技术研发中心>adb pair  192.168.110.89:46419
Enter pairing code: 425945         # code 输入对应的code
Successfully paired to 192.168.110.89:46419 [guid=adb-10CF4P0UGY002CU-lLcDqW]
```

## 获取手机上的位置坐标方案

### 显示触摸位置和坐标

在手机上开启「开发者选项」中的：

- **指针位置**（显示触摸点坐标）
- **显示触摸操作**（可视化触摸反馈）

开启后手动点击目标位置，屏幕会显示该点的实时坐标，记下坐标后再用 ADB 命令模拟点击：

```bash
adb shell input tap 坐标X 坐标Y
```

### 通过元素定位（更精准）

如果是针对特定 APP 操作，可以使用`uiautomator`工具分析界面元素：

```bash
# 生成当前界面的UI层次结构文件
adb shell uiautomator dump /sdcard/ui.xml

# 将文件导出到电脑
adb pull /sdcard/ui.xml ./
```

打开导出的`ui.xml`文件，查找目标元素的`bounds`属性（如`bounds="[x1,y1][x2,y2]"`），取中间点坐标`((x1+x2)/2, (y1+y2)/2)`作为点击位置。

### 使用可视化工具辅助

推荐使用这类工具直观定位坐标：

- **Scrcpy**：投屏到电脑，鼠标悬停时会显示实时坐标
- **Android Studio 的 Layout Inspector**：精确查看 UI 元素位置
- **ADB Tools GUI**：部分图形化工具支持点击屏幕直接生成 ADB 命令