## 事情原因

> **20260720** 今天上班突然发现电脑非常卡  查看进程 有一个 **jc5cYQ58.exe**的exe文件占用内存**93%**，
>
> 右键打开文件发现没有软件
>
> 很会隐藏吗



### 手动删除(发现删除之后还会启动-进一步排查)

**管理员模式打开cmd**

**逐条执行搜索命令，找出病毒所有分身**

```bash
# 搜索所有随机exe病毒1
where /R C:\ JZXuqT.exe
where /R C:\ qa3PKn.exe
where /R C:\ jc5cYQ58.exe

# 搜索病毒文件夹（查找全部残留目录）
dir /s /ad /b C:\ | findstr "3bRwC0"
dir /s /ad /b C:\ | findstr "NucSCa"
dir /s /ad /b C:\ | findstr "8UMbrSew"
```

**一键删除整个病毒文件夹（无需进目录查看）**

```bash
rd /s /q "C:\ProgramData\8UMbrSew"
```

### 排查定时任务(删除之后重启还有)

> 手动删除目录之后 还是会启动 那肯定跟定时任务有关 所以准备排查一下系统的**任务计划程序**  但是打开 **任务计划程序** 就会闪退

问题根源：挖矿病毒破坏了 MMC 控制台组件 + 篡改计划任务服务注册表，导致 taskschd.msc 打开瞬间闪退

前置：先通过命令行批量查杀所有恶意定时任务（不用打开图形界面）

以**管理员身份**打开 PowerShell，直接执行批量检索 + 删除命令，绕过损坏的图形窗口：

```bash
# 1. 列出所有含病毒目录的定时任务
Get-ScheduledTask | Where-Object {
    $_.Actions.Execute -match "8UMbrSew|3bRwC0|NucSCa|jc5cYQ58|JZXuqT|qa3PKn"
} | Format-Table TaskName,TaskPath,Actions

# 2. 一键删除所有挖矿相关定时任务（无确认弹窗） 有效
Get-ScheduledTask | Where-Object {
    $_.Actions.Execute -match "8UMbrSew|3bRwC0|NucSCa|jc5cYQ58|JZXuqT|qa3PKn"
} | Unregister-ScheduledTask -Confirm:$false
```

执行完无报错 = 恶意定时任务已全部清除。

方案 1：修复 MMC 控制台（最常见闪退原因，病毒篡改 SnapIn 注册表）

Win+R 输入`regedit`打开注册表

定位路径：

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\MMC\SnapIns
```

右侧查找两个固定项，右键删除：

```bash
FX:{c7b8fb06-bfe1-4c2e-9217-7a69a95bbac4}
FX:{c7b8fb07-bfe1-4c2e-9217-7a69a95bbac4}
```

> 识别特征：项内`AssemblyName`值为`TaskScheduler`

1. 删除后重启电脑，再打开`taskschd.msc`测试。

方案 2：修复任务计划核心服务（病毒禁用 / 损坏 Schedule 服务）

步骤 1：管理员 CMD 重新注册任务计划 DLL

```bash
regsvr32 /u /s taskschd.dll
regsvr32 /i /s taskschd.dll
regsvr32 schtasks.dll
```

步骤 2：修复 Schedule 服务并启动

1. Win+R 输入`services.msc`打开服务列表

2. 找到

   Task Scheduler

   （任务计划程序）

   - 启动类型：改为**自动**
   - 点击「启动」，若提示启动失败继续下一步

3. CMD 修复服务注册表权限：

```cmd
sc config Schedule start=auto
net start Schedule
```

方案 3：系统文件完整性修复（病毒篡改系统组件）

管理员 CMD 依次执行两条系统修复命令：

```cmd
# 修复所有系统文件
sfc /scannow
# 修复系统镜像底层损坏
DISM /Online /Cleanup-Image /RestoreHealth
```

两条命令全部跑完后重启电脑。

方案 4：安全模式兜底清理（病毒持续篡改系统配置）

1. 断开 WiFi / 网线，阻止病毒联网修复自身

2. 进入**不带网络的安全模式**

3. 重复执行：

   1. PowerShell 删除恶意定时任务
   2. 删除 MMC 异常注册表项
   3. CMD 删除全部病毒文件夹：

   ```cmd
   taskkill /f /im jc5cYQ58.exe /im JZXuqT.exe /im qa3PKn.exe
   rd /s /q "C:\ProgramData\8UMbrSew"
   rd /s /q "C:\Program Files (x86)\3bRwC0"
   rd /s /q "C:\Users\Public\NucSCa"
   ```

4. 删除注册表两处恶意自启动项：

   

   ```
   HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Run
   ```

   

   删除

   ```
   Alibaba SecurityHealtha
   ```

   ```
   Tencent SecurityHealth
   ```

5. 重启进正常系统，打开`taskschd.msc`验证是否恢复。

方案 5：替代工具（暂时不用修复 taskschd.msc，可视化查杀启动项）

如果暂时不想修复系统，直接用**火绒安全**替代，一键查看所有定时任务、注册表启动、系统服务：

1. 打开火绒 → 【安全工具】→【启动项管理】
2. 切换「计划任务」标签，所有乱码、指向病毒 exe 的任务直接右键删除
3. 同时清理「注册表启动」「系统服务」里的恶意项，全程不依赖 Windows 自带任务计划程序。



### 排查是不是有父进程(有)

两种方法查 jc5cYQ58.exe 父进程（病毒母体溯源）

方法 1：管理员 PowerShell（推荐，直接输出父进程 PID / 名称）

1. 右键开始菜单 → 打开 **Windows PowerShell (管理员)**
2. 执行这条完整查询命令：

```powershell
Get-WmiObject Win32_Process -Filter "Name='jc5cYQ58.exe'" | Select-Object Name,ProcessId,ParentProcessId
```

结果解读

- 输出 `ParentProcessId` 后面的数字 = 父进程 PID
- 再用 PID 查父进程名称（把数字替换成你查到的 PID）：

```powershell
Get-WmiObject Win32_Process -Filter "ProcessId=这里填PID数字" | Select-Object Name,ExecutablePath
```

两种高危结果判断

1. 父进程是 `explorer.exe` / `cmd.exe` / `powershell.exe`：母体藏在开机启动、计划任务、临时目录
2. 父进程是另一串随机 exe（如`qa3PKn.exe`/`JZXuqT.exe`）：这是**病毒主母体**，它负责不停生成 jc5cYQ58.exe，只删子进程没用，必须干掉父程序
3. 父进程 PID=0：进程由系统服务 / 驱动启动，需要去系统服务里找恶意项

**查询 PID=1272 的父进程完整信息**

管理员 PowerShell 执行：

```powershell
Get-WmiObject Win32_Process -Filter "ProcessId=1272" | Select-Object Name,ProcessId,ExecutablePath,ParentProcessId
```

这条命令会输出：父进程文件名、完整文件路径、父进程的父 PID（溯源到源头）