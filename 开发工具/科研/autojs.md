# 说明

> 我们玩手机的时候经常会做一些重复的事情但是又不想自己手动的去做
>
> 例如朋友圈点赞,这时候就想着如果能有一个脚本帮我们完成这种没有意义
>
> 的工作那该多好

## 资料

| 名称                                | 地址                                         |
| ----------------------------------- | -------------------------------------------- |
| autojspro-官网                      | [link](https://pro.autojs.org/)              |
| api文档                             | [link](https://pro.autojs.org/docs/#/zh-cn/) |
| 官方文档                            | [link](http://autojs.cc/)                    |
| autojs-博客                         | [link](https://blog.autojs.org/)             |
| 阿里巴巴-矢量图库(可以用来下载图标) | [link](https://www.iconfont.cn/)             |

# 下载autojspro



| 名称                         | 地址                                                         |
| ---------------------------- | ------------------------------------------------------------ |
| github  对应自己保存外链下载 | [link](https://github.com/SuperMonster003/AutoJs6/releases)   [link](https://yaoliuyang.lanzoui.com/iFI3awg65hg) |
|                              |                                                              |

## 用PC 调试开发 AutoJS 

[link](https://blog.csdn.net/qq_16946803/article/details/121339840#:~:text=%E6%9C%AC%E6%96%87%E6%A1%A3%E8%AF%A6%E7%BB%86%E4%BB%8B%E7%BB%8D%E4%BA%86%E5%A6%82%E4%BD%95%E9%85%8D%E7%BD%AEVisual%20Studio%20Code%20%28VSCode%29,%E4%B8%8E%20Auto.js%20%E8%BF%9B%E8%A1%8C%E9%9B%86%E6%88%90%E5%BC%80%E5%8F%91%EF%BC%8C%E5%8C%85%E6%8B%AC%E5%9C%A8%E7%94%B5%E8%84%91%E7%AB%AF%E5%AE%89%E8%A3%85%E6%89%80%E9%9C%80%E6%8F%92%E4%BB%B6%EF%BC%8C%E5%90%AF%E5%8A%A8%E6%9C%8D%E5%8A%A1%EF%BC%8C%E4%BB%A5%E5%8F%8A%E5%9C%A8%E6%89%8B%E6%9C%BA%E7%AB%AF%E5%AE%89%E8%A3%85Auto.js%EF%BC%8C%E8%AE%BE%E7%BD%AE%E6%97%A0%E9%9A%9C%E7%A2%8D%E6%9D%83%E9%99%90%E3%80%82%20%E6%AD%A4%E5%A4%96%EF%BC%8C%E5%AF%B9%E4%BA%8E%E7%9C%9F%E6%9C%BA%E8%AE%BE%E5%A4%87%EF%BC%8C%E8%BF%98%E6%8F%90%E4%BE%9B%E4%BA%86%E9%A2%9D%E5%A4%96%E7%9A%84%E9%85%8D%E7%BD%AE%E6%AD%A5%E9%AA%A4%EF%BC%8C%E5%A6%82%E4%BD%BF%E7%94%A8%E6%89%8B%E6%9C%BA%E6%8A%95%E5%B1%8F%E5%B7%A5%E5%85%B7%E5%92%8C%E5%BC%80%E5%90%AFUSB%E8%B0%83%E8%AF%95%E3%80%82%20%E6%9C%80%E5%90%8E%EF%BC%8C%E9%80%9A%E8%BF%87%E7%BC%96%E5%86%99%E7%AE%80%E5%8D%95%E7%9A%84js%E8%84%9A%E6%9C%AC%E5%B9%B6%E8%BF%9B%E8%A1%8C%E6%B5%8B%E8%AF%95%EF%BC%8C%E7%A1%AE%E4%BF%9DVSCode%E4%B8%8EAuto.js%E7%9A%84%E8%BF%9E%E6%8E%A5%E6%88%90%E5%8A%9F%E3%80%82)     [vscode官网下载](https://code.visualstudio.com/)

### 准备工作

- 电脑端

  - 安装 VSCode（Visual Studio Code），它是常用的代码编辑器。
  - 在 VSCode 中安装相关插件，如 “Auto.js-Autox.js-VSCodeExt”（如果使用 autoX）或 “Auto.js-VSCodeExt”“Auto.js-Pro-Ext”（根据具体情况选择，Pro 版相关插件用于 Pro 版开发等）12。

- 手机端

  - 安装 AutoJS 应用（可以使用模拟器，也可以用真机；若用真机，需确保系统兼容，如 AutoJS 4.1.1 等版本适用于部分 Android 系统）。
  - 若用真机，需进行以下操作：
    - 打开手机 “设置”，找到 “关于手机” 或 “关于设备”，连续点击 “版本号” 七次，进入开发者模式。
    - 返回设置主菜单，找到 “系统” 或 “连接与共享” 等（不同手机路径不同）下的 “开发者选项”，启用 “USB 调试”。还可根据需要启用 “Stay awake”（保持唤醒）等有助于调试的选项4。

  ### 连接电脑与手机

  - 通过 ADB 连接（常见且稳定的方式）
    - 在电脑上下载并安装 ADB 工具（ADB 是 Android Debug Bridge，一般包含在 Android SDK 中，也可独立下载），并将其路径添加到系统环境变量中（Windows：在 “系统属性” 的 “环境变量” 中编辑 “Path” 变量添加；Mac/Linux：通过编辑`.bash_profile`或`.zshrc`文件添加，如`export PATH=$PATH:/path/to/adb`，然后执行`source`命令使其生效）。
    - 用 USB 数据线将手机连接到电脑，手机上弹出询问是否允许 USB 调试的提示时，选择 “允许”。
    - 打开命令提示符（Windows）或终端（Mac/Linux），输入`adb devices`，若设备连接成功，会显示设备的序列号和 “device” 状态。
    - 在手机上启动 Auto.js 应用，在命令行中输入`adb forward tcp:8000 tcp:8000`（部分情况可能不需要此步，具体看 Auto.js 应用内的设置和需求）。
  - 通过局域网连接（无线方式，需在同一局域网）
    - 手机进入 “设置”->“Wi-Fi”，点击当前连接的 Wi-Fi 网络，记下设备的 IP 地址。
    - 打开 Auto.js 应用，进入 “开发者工具”->“局域网调试”，记下显示的端口号（默认为 38888 等，不同版本可能不同，也可自定义）。
    - 在电脑的命令行中输入`adb connect <设备 IP 地址>:<端口号>`，例如`adb connect 192.1.1.100:38888`，连接成功后设备会显示在`adb devices`列表中，状态为 “device”。

  ### 启动服务与创建调试脚本

  - 在 VSCode 中，通过点击搜索栏或按`Ctrl+Shift+P`快捷键，搜索 “autojs” 并启动服务，记下右下角显示的 IP 地址（若有，部分情况可能不需要此 IP 地址，主要是确保服务启动）1。
  - 在 VSCode 中创建新的 js 文件，编写 AutoJS 脚本代码，比如简单的`toast('哈喽 第一个应用')`用于测试，或编写更复杂的自动化脚本，如涉及界面元素操作等的代码2。
  - 保存脚本文件后，按`F5`键或点击 VSCode 右上角的运行按钮来运行脚本12。
  - 调试时，可以在脚本中合适的位置设置断点（如果 VSCode 插件支持相关调试功能的话，不同插件可能设置方式略有不同，一般可在代码行号旁点击设置），然后运行脚本，当脚本执行到断点处会暂停，此时可查看变量值、执行栈等信息来排查问题。如果是简单的查看日志等，也可通过在脚本中使用`log()`等函数输出信息，然后在 VSCode 的控制台或 AutoJS 应用的日志查看区域（如果有）查看相关信息。

  ### 其他注意事项

  - 确保 AutoJS 版本和 ADB 工具版本匹配，以免出现兼容性问题。
  - 若连接出现问题，可尝试重新启动手机、电脑、ADB 服务（命令行输入`adb kill-server`后再输入`adb start-server`），检查 USB 连接、网络连接，查看电脑防火墙或安全软件是否阻止连接等4。
  - 对于一些复杂的调试场景，可能还需要结合 AutoJS 应用本身的一些调试功能和日志信息，以及手机系统的开发者选项中的相关调试设置（如查看运行中的服务、查看日志输出等，不同手机操作略有不同）来综合分析和解决问题。



# 脚本实践

###  自动刷视频

```shell
auto.waitFor();
toast("开始刷视频");

function swipeUp() {
    let x = device.width / 2;
    let y_start = device.height * 0.75;
    let y_end = device.height * 0.35;
    let duration = random(300, 800);
    swipe(x, y_start, x, y_end, duration);
}

// 在新线程中执行刷视频逻辑
threads.start(function() {
    while (true) {
        swipeUp();
        let delay = random(5000, 15000); // 5~15秒
        toast("等待 " + Math.floor(delay / 1000) + " 秒...");
        sleep(delay);
    }
});

```

**自动刷视频脚本模拟真人**

```js
// 导入必要模块
auto.waitFor(); // 等待无障碍服务启动

// 设置随机数函数，用于生成8-15秒的随机等待时间
function randomTime(min, max) {
    return Math.floor(Math.random() * (max - min + 1)) + min;
}

// 设置随机数函数，用于生成随机滑动距离
function randomSwipeDistance() {
    return Math.floor(Math.random() * 300) + 400; // 生成400-700之间的随机数
}

// 设置随机数函数，用于生成随机概率
function randomChance(percent) {
    return Math.random() < percent;
}

// 获取屏幕尺寸
let deviceWidth = device.width;
let deviceHeight = device.height;

// 主函数
function main() {
    toast("开始自动刷视频");
    let videoCount = 0;
    
    while (true) {
        videoCount++;
        toast("正在观看第 " + videoCount + " 个视频");
        
        // 随机等待8-15秒
        let waitTime = randomTime(8, 15);
        log("等待时间: " + waitTime + " 秒");
        sleep(waitTime * 1000);
        
        // 随机执行点赞操作(10%的概率)
        if (randomChance(0.1)) {
            log("执行点赞操作");
            click(deviceWidth * 0.9, deviceHeight * 0.7);
            sleep(1000);
        }
        
        // 随机执行评论操作(5%的概率)
        if (randomChance(0.05)) {
            log("执行评论操作");
            click(deviceWidth * 0.5, deviceHeight * 0.85);
            sleep(1000);
            
            // 随机选择一条评论
            let comments = ["很不错", "这个视频太精彩了", "学到了很多", "666", "加油！"];
            let randomComment = comments[Math.floor(Math.random() * comments.length)];
            
            // 输入评论
            setText(randomComment);
            sleep(1000);
            
            // 发送评论
            click("发送");
            sleep(1000);
        }
        
        // 随机执行分享操作(3%的概率)
        if (randomChance(0.03)) {
            log("执行分享操作");
            click(deviceWidth * 0.85, deviceHeight * 0.8);
            sleep(1000);
            
            // 随机选择一个分享渠道
            click("复制链接");
            sleep(1000);
            
            // 返回
            back();
            sleep(1000);
        }
        
        // 随机滑动(90%的概率下滑，10%的概率上滑)
        if (randomChance(0.9)) {
            // 下滑到下一个视频
            log("下滑到下一个视频");
            swipe(deviceWidth * 0.5, deviceHeight * 0.8, deviceWidth * 0.5, deviceHeight * 0.2, randomTime(300, 800));
        } else {
            // 上滑查看更多内容
            log("上滑查看更多内容");
            swipe(deviceWidth * 0.5, deviceHeight * 0.2, deviceWidth * 0.5, deviceHeight * 0.8, randomTime(300, 800));
        }
        
        // 随机等待一段时间，模拟人类操作
        sleep(randomTime(1, 3) * 1000);
    }
}

// 启动主函数
main();
```

