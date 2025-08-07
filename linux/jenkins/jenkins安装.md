#  docker安装(强烈推荐)

## **docker-compose.yml** 安装

```yaml
version: '3'
services:
  jenkins:
    image: jenkins/jenkins:lts  # 官方长期支持版
    container_name: jenkins
    restart: always  # 自动重启
    ports:
      - "8080:8080"  # 访问端口
      - "50000:50000"  # 代理端口
    volumes:
      - jenkins_data:/var/jenkins_home  # 持久化数据卷（关键）
    environment:
      - TZ=Asia/Shanghai  # 时区设置
    user: root  # 避免权限问题（可选，视服务器情况）
volumes:
  jenkins_data:  # 定义数据卷，会自动创建
```

##  补充

###  docker版本的jenkins迁移到别的服务器

> 假如我们需要把A服务器的docker迁移到B服务器中  还要重新搭建一遍吗

####   手动备份恢复

> 找到定义的数据卷并复制到本地  备份 手动上传或者远程复制到迁移的B服务器

```shell
# 查看数据券详情(可以用以下命令找到 jenkins_data 的实际路径（不是必须，只是想看一眼可以）：)
docker volume inspect jenkins_data

# 你使用的是 docker volume 保存 Jenkins 数据，而不是直接挂载主机目录（从你的 yml 来看是这样）：

volumes:
  - jenkins_data:/var/jenkins_home
  
# 解释:Docker 会把这个 jenkins_data 卷存储在宿主机的默认卷目录下（通常是 /var/lib/docker/volumes/jenkins_data/_data）。 这个路径就是你要打包的 Jenkins 数据目录。

##############################################################################################

# 压缩备份数据(手动 ps:进入数据目录 将 Jenkins 所有配置和构建数据打包到 /root/jenkins_backup.tar.gz)
cd /var/lib/docker/volumes/jenkins_data/_data
tar -czvf /root/jenkins_backup.tar.gz .


# 拷贝备份文件到 B 服务器
scp /root/jenkins_backup.tar.gz user@B_SERVER_IP:/root/


# 创建数据卷 B服务器(确保你的 docker-compose 使用的是同样的卷名：)
docker volume create jenkins_data

# 找到新卷的数据目录
docker volume inspect jenkins_data
# 假设你得到的是：
/var/lib/docker/volumes/jenkins_data/_data

# 解压备份到这个目录(这一步会把原来 Jenkins 所有数据还原到新的卷目录中。)
cd /var/lib/docker/volumes/jenkins_data/_data
tar -xzvf /root/jenkins_backup.tar.gz

# 启动 Jenkins 服务(回到你的 docker-compose.yml 文件所在目录，执行：)
docker-compose up -d
```

#### 懒人实现

> 直接找到**/var/lib/docker/volumes/jenkins_data/_data** 中的目录  把**_data** 中的所有数据打包为**jenkins_home.tar.gz**  然后解压到
>
> 数据目录**/data/jenkins/jenkins_home**  直接启动就行了

```shell
docker run -d \
  --name jenkins \
  -p 8080:8080 -p 50000:50000 \
  -v  /data/jenkins/jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```



