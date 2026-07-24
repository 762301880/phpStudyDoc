## [w3cschool批处理入门手册](https://www.w3cschool.cn/pclrmsc/)

## bat命令实战

### 一键开机启动多款软件 BAT 脚本教程

完整可用 bat 代码（可直接修改）

新建文本文档，把下面代码复制进去，修改里面软件路径，最后后缀改为 `.bat` 双击就能一次性打开所有程序

```bash
@echo off
chcp 65001 >nul
echo 正在一键启动常用软件...
echo ==========================

:: 格式：start "" "软件完整路径"
:: 下面按需增删每一行
start "" "D:\Program Files\QQ\Bin\QQScLauncher.exe"
start "" "D:\Software\WeChat\WeChat.exe"
start "" "D:\Program Files (x86)\Google\Chrome\Application\chrome.exe"
start "" "D:\Program Files\Notepad++\notepad++.exe"
start "" "D:\Program Files\VS Code\Code.exe"

echo.
echo 所有软件启动完毕！
timeout /t 2 /nobreak >nul
exit
```

#### 怎么获取软件正确路径

**方法 1（最简单）**

1. 桌面找到软件快捷方式 → 右键【属性】
2. **目标**那一栏的全部内容，就是完整路径，复制替换脚本里引号内内容即可

**方法 2**

右键软件图标 → 打开文件所在位置，地址栏 + 选中的 exe 文件名拼接起来就是完整路径



#### 设置开机自动运行（开机不用手动点 bat）

两种任选一种：

##### 方式 1：放入开机启动文件夹（最推荐）

1. 快捷键 `Win + R` 输入：`shell:startup` 回车，打开**开机启动目录**

2. 把你做好的 

   ```
   .bat
   ```

    文件复制粘贴进去

   

   下次电脑开机，系统会自动运行这个脚本，一键打开所有软件

##### 方式 2：任务计划程序（后台静默启动，不弹出黑窗口）

适合不想开机闪出命令行黑框的需求：

1. Win 键搜索「任务计划程序」打开
2. 创建基本任务 → 名称填：一键启动软件
3. 触发器选择：**当计算机启动时**
4. 操作：启动程序 → 选择你的 bat 文件
5. 勾选「不管用户是否登录都要运行」即可

#### 进阶优化：运行不弹出黑色 CMD 窗口

新建另一个文本文档，后缀改为 `.vbs`，和 bat 文件放同一文件夹

```bash
Set ws = CreateObject("WScript.Shell")
ws.Run "启动软件.bat",0
```





