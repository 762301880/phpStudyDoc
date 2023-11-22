##   执行命令目录太长报错/usr/bin/cd: line 2: cd: too many arguments

```php
[program:sbt_easy_task]
process_name=%(program_name)s_%(process_num)02d
directory=/www/wwwroot/sbt.51wpu.com.cn/     # 可以设置一个命令执行目录
command=php think task start
autostart=true
autorestart=true
startsecs=0
# 修改为当前登录的用户 可以用 whoami 命令查询
user=root 
numprocs=1
redirect_stderr=true
stdout_logfile=/etc/supervisord.d/log/sbt_easy_task.log 
stopwaitsecs=3600

# EasyTask.php 定时脚本备份
```

