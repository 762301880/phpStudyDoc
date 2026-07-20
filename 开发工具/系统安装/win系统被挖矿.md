## 事情原因

> **20260720** 今天上班突然发现电脑非常卡  查看进程 有一个 **jc5cYQ58.exe**的exe文件占用内存**93%**，
>
> 右键打开文件发现没有软件
>
> 很会隐藏吗



## Windows 挖矿木马 jc5cYQ58.exe 排查清理记录

## 1. 症状表现

- 电脑重启后，木马文件自动重新出现。
- 安全软件可以检测，但无法彻底清除。
- 手动删除后，重启系统又恢复。
- 删除过程中出现：
  - 拒绝访问
  - 文件正在被其他程序使用

主要异常文件：

C:\ProgramData\8UMbrSew\jc5cYQ58.exe

---

## 2. 初步定位病毒文件

使用命令：

```cmd
where /r C:\ jc5cYQ58.exe
```

发现：

```
C:\ProgramData\8UMbrSew\jc5cYQ58.exe
C:\Users\All Users\8UMbrSew\jc5cYQ58.exe
```

说明病毒位于：

```
C:\ProgramData\8UMbrSew
```

`C:\Users\All Users` 实际为 `C:\ProgramData` 的兼容映射。

------

## 3. 发现关联启动程序

检查注册表启动项：

```
reg query HKLM\Software\Microsoft\Windows\CurrentVersion\Run
```

发现：

```
Alibaba SecurityHealtha
C:\Program Files (x86)\UNEi1qV3k\z4SzsX2.exe
```

异常目录：

```
C:\Program Files (x86)\UNEi1qV3k
```

包含：

```
image.png
thumbs.db
XPSPLOG.dll
z4SzsX2.exe
```

特点：

- 随机目录名
- 随机文件名
- 伪装安全软件名称
- 开机自动启动

------

## 4. 处理文件权限保护

删除时发现：

```
拒绝访问
```

检查 ACL：

```
icacls "C:\Program Files (x86)\UNEi1qV3k"
```

发现：

```
BUILTIN\Users:(OI)(CI)(DENY)(S,DC)
```

说明木马修改了 NTFS 权限：

- 添加 DENY 删除权限
- 阻止管理员删除

处理：

```
icacls "C:\Program Files (x86)\UNEi1qV3k" /remove:d Users
```

恢复删除权限。

------

## 5. 定位木马运行进程

查看进程：

```
tasklist | findstr /i "jc5cYQ58"
```

发现：

```
jc5cYQ58.exe PID 3376
```

查询父进程：

```
wmic process where name="jc5cYQ58.exe" get ParentProcessId
```

发现：

```
ParentProcessId 1408
```

继续查询：

```
tasklist /svc | findstr "1408"
```

结果：

```
svchost.exe 1408 Schedule
```

判断：

木马通过 Windows 计划任务服务启动。

------

## 6. 查找计划任务复活点

导出任务：

```
schtasks /query /fo LIST /v > C:\schtasks.txt
```

搜索：

```
findstr /i "8UMbrSew jc5cYQ58 ProgramData" C:\schtasks.txt
```

发现：

```
C:\ProgramData\RmMUPbVs
    jc5cYQ58.exe 1776

C:\ProgramData\8UMbrSew
    jc5cYQ58.exe 1776
```

进一步使用 PowerShell 查询任务动作：

```
Get-ScheduledTask | ForEach-Object {
    foreach($action in $_.Actions){
        if(($action.Execute + $action.Arguments + $action.WorkingDirectory) -match "RmMUPbVs|8UMbrSew|jc5cYQ58"){
            Write-Host "任务:" $_.TaskPath$_.TaskName
            $action
        }
    }
}
```

发现两个恶意计划任务：

### 木马任务1

```
任务:
\ Elevate Our Plan

程序:
1cKcu8KR.exe

工作目录:
C:\ProgramData\RmMUPbVs
```

作用：

负责释放/恢复木马。

### 木马任务2

```
任务:
\ Sustainability Standardization Data Optimization Seamlessly

程序:
jc5cYQ58.exe

参数:
1776

工作目录:
C:\ProgramData\8UMbrSew
```

作用：

启动木马程序。

------

## 7. 清理步骤

### 删除计划任务

CMD：

```
schtasks /delete /tn "\Elevate Our Plan" /f
schtasks /delete /tn "\Sustainability Standardization Data Optimization Seamlessly" /f
```

------

### 结束木马进程

```
taskkill /f /im jc5cYQ58.exe
taskkill /f /im 1cKcu8KR.exe
```

------

### 删除木马目录

处理：

```
C:\ProgramData\RmMUPbVs
```

和：

```
C:\ProgramData\8UMbrSew
```

命令：

```
takeown /f "目录路径" /r /d y
icacls "目录路径" /grant Administrators:F /t
rmdir /s /q "目录路径"
```

------

## 8. 最终验证

检查计划任务：

```
schtasks /query /fo LIST | findstr /i "RmMUPbVs 8UMbrSew jc5cYQ58 1cKcu8KR"
```

无输出。

检查文件：

```
where /r C:\ jc5cYQ58.exe
```

无结果。

确认：

- 重启后木马不再出现。
- 计划任务复活链已删除。
- 木马文件已清理。

------

## 9. 木马行为总结

完整感染链：

```
恶意计划任务
        |
        |
        v
C:\ProgramData\RmMUPbVs\1cKcu8KR.exe
        |
        |
        v
释放/恢复
        |
        |
        v
C:\ProgramData\8UMbrSew\jc5cYQ58.exe
        |
        |
        v
挖矿木马运行
```

木马使用技术：

- 随机目录名隐藏
- 随机 exe 文件名
- 伪装安全软件名称
- Windows 计划任务持久化
- 修改 NTFS ACL 防止删除
- 使用 ProgramData 隐藏运行

------

## 10. 后续防护建议

1. 开启 Windows Defender 离线扫描。
2. 检查计划任务：

```
schtasks /query /fo LIST /v
```

重点关注：

- ProgramData
- AppData
- 随机目录
- 无描述程序

1. 定期检查启动项：

```
wmic startup get caption,command
```

1. 不运行来源不明破解软件、激活工具。
2. 发现随机目录：

例如：

```
C:\ProgramData\xxxxxx
C:\Program Files (x86)\xxxxxx
```

且包含随机 exe，应优先排查。

```
这个记录以后遇到类似 **“重启复活型木马”** 可以直接照这个流程走。此次核心经验就是：

**不要先删文件，先找持久化入口。这个案例真正的根源是计划任务，而不是 jc5cYQ58.exe 本身。**
```