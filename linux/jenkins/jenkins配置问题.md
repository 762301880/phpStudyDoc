# jenkins删除全局凭据

## 参考资料

https://cloud.tencent.com/developer/article/1605104

## 操作步骤

> 配置东西的时候经常会看见一些凭据重复了这里如何删除呢

![1640758700(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/yBQJftLuzq4EUM5.png)

**1.点击用户-凭据**

![1640758808(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/9K1a6j4cx5pheWg.png)

**2.删除凭据**

![1640758870(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/X6J8GpOrvInQtyP.png)

#  docker-jenkins升级

> 很烦就是软件的内部消息推送如下图所示，于是为了不看到这个烦人的标记想着把jenkins升级一下

![1647311622(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/kaXu2Mo4OwsGiUh.png)





**下载jenkins.war包**

> 由于官网提供的下载速度太慢所以我们去[清华大学镜像站](https://mirrors.tuna.tsinghua.edu.cn/jenkins/war/)下载镜像

![1647312013(1).jpg](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/RTYo93OzLe6MbiE.png)

**下载镜像到服务器内部**

> 直接右键复制链接 然后使用**wget** 命令下载到服务器

```shell
wget https://mirrors.tuna.tsinghua.edu.cn/jenkins/war/2.338/jenkins.war
```

**将下载的war包放到容器中**

```shell
[root@VM-16-5-centos ~]# ls
jenkins.war

[root@VM-16-5-centos ~]# docker ps -a | grep jenkins
57982f049fb4        46                                      "/sbin/tini -- /us..."   4 weeks ago         Up About an hour          0.0.0.0:8080->8080/tcp, 0.0.0.0:50000->50000/tcp   jenkins
# 使用cp 命令将本地文件拷贝到容器内部
[root@VM-16-5-centos ~]# docker cp jenkins.war  jenkins:/jenkins.war
# 删除服务器war包
[root@VM-16-5-centos ~]# rm -rf jenkins.war
# 以root用户进入jenkins 内部 因为默认的是普通用户我们需要删除旧的 jenkins.war文件
[root@VM-16-5-centos ~]# docker exec -it -u root jenkins bash
root@57982f049fb4:/# ls
bin   dev  home		lib    media  opt   root  sbin	sys  usr
boot  etc  jenkins.war	lib64  mnt    proc  run   srv	tmp  var
# 查询 旧的war包所在的位置
root@57982f049fb4:/# find / -name jenkins.war
/usr/share/jenkins/jenkins.war
find: ‘/proc/1/map_files’: Operation not permitted
find: ‘/proc/6/map_files’: Operation not permitted
find: ‘/proc/180/map_files’: Operation not permitted
find: ‘/proc/227/map_files’: Operation not permitted
find: ‘/proc/233/map_files’: Operation not permitted
/jenkins.war
# 这里删除旧包或者备份(推荐备份  mv /usr/share/jenkins/jenkins.war /usr/share/jenkins/jenkins.war.cp)
root@57982f049fb4:/# rm -rf /usr/share/jenkins/jenkins.war
# 移动新的安装包到war包目录
root@57982f049fb4:/# mv jenkins.war /usr/share/jenkins/
root@57982f049fb4:/# ls /usr/share/jenkins/
jenkins.war  ref
# 退出容器并重启jenkins
root@57982f049fb4:/# exit
exit
[root@VM-16-5-centos ~]# docker restart jenkins

# 最后重新打开jenkins外网地址完成升级
```

#  jenkins重启

> 地址+restart  

```shell
# 例子
http://81.69.231.252:8080/restart
```

#  初始用户admin密码

> 初始用户admin是没有密码的，可以用初始界面的密码进行登录,然后再设置中修改密码

```shell
# 查看初始密码
docker exec -it jenkins cat /var/jenkins_home/secrets/initialAdminPassword
```



#  陌生用户访问jenkins地址必须是登录界面设置

> **系统管理->全局安全配置**  设置为**登录用户才可以做任何事**  把**匿名用户具有可读权限关了(不然陌生用户也可以看到你的项目)**

![image-20250803142422094](https://gitee.com/yaolliuyang/blogImages/raw/master/blogImages/image-20250803142422094.png)





