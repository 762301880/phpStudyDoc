## 事情原因

> **20260720** 今天上班突然发现电脑非常卡  查看进程 有一个 **jc5cYQ58.exe**的exe文件占用内存**93%**，
>
> 右键打开文件发现没有软件
>
> 很会隐藏吗



### 手动删除

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

