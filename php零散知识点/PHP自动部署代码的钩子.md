# [搬运](https://mp.weixin.qq.com/s/0pRQ9VG9opQ7rRDEq_uvWA)

自动化部署代码是一个常见的任务，可以通过编写脚本或使用自动化工具来完成。

**下面是一些常见的方法：**

**1.使用CI/CD工具**

CI/CD工具（例如Jenkins，Travis CI，GitLab CI/CD等）可以与Git版本控制系统集成，根据触发条件自动构建，测试和部署代码。这种方法的优点是灵活性和可扩展性。

**2.使用Shell脚本**

可以编写Shell脚本来自动化代码部署。这种方法需要一定的Linux命令行知识，但是可以灵活地定制脚本来满足具体的部署需求。



**3.使用PHP脚本**

如果您的Web应用程序使用PHP编写，则可以编写PHP脚本来自动化代码部署。这种方法通常需要SSH访问权限，并且需要一定的PHP编程知识。

以下是一个基本的PHP自动部署脚本的示例：

```php
<?php
// 通过SSH连接到远程服务器
$ssh = ssh2_connect('example.com', 22);
ssh2_auth_password($ssh, 'username', 'password');

// 切换到代码库目录
ssh2_exec($ssh, 'cd /var/www/myapp');

// 拉取最新代码
ssh2_exec($ssh, 'git pull origin master');

// 安装依赖项
ssh2_exec($ssh, 'composer install');

// 运行数据库迁移
ssh2_exec($ssh, 'php artisan migrate');

// 重启Web服务器
ssh2_exec($ssh, 'sudo service apache2 restart');
```

这个脚本使用PHP内置的SSH2扩展连接到远程服务器，然后执行一系列命令来更新和部署代码。

需要注意的是，自动部署代码需要小心谨慎，必须确保部署脚本能够处理错误，不会导致应用程序不可用或数据丢失。建议在部署之前备份所有数据，并在测试环境中测试自动部署脚本。



**4.常见的钩子包括：pre-commit、pre-push、post-merge等等。**

编写自动部署代码的`Git hook`，可以考虑使用`post-receive`钩子，该钩子会在代码推送到远程仓库之后触发。具体步骤如下：

- 进入Git仓库所在的服务器，切换到仓库所在的目录。
- 在.git/hooks目录下创建一个名为post-receive的文件，该文件为Shell脚本。
- 将需要执行的自动部署代码添加到post-receive文件中，

例如

```php
#!/bin/bash

# 切换到仓库目录
cd /path/to/repository

# 拉取最新代码
git fetch origin
git reset --hard origin/master

# 执行自动部署脚本
./deploy.sh
```

给post-receive文件添加可执行权限，例如：

```shell
chmod +x post-receive
```

测试自动部署钩子是否生效，可以手动推送一次代码到远程仓库，观察是否执行了自动部署代码。

需要注意的是，`post-receive`钩子会在每次代码推送到远程仓库之后触发，因此在编写自动部署脚本时需要特别小心，避免在错误的时间或情况下执行部署操作。同时，还需要确保自动部署脚本能够正确地执行，避免出现意外的错误或问题。

##  bug解析

### 报错 Call to undefined function App\Http\Controllers\ssh2_connect() 

> 这个错误提示是因为在App\Http\Controllers中调用了一个未定义的函数ssh2_connect()。要解决这个问题，你需要确保已经安装了php-ssh2扩展，并在代码中引入了正确的命名空间。

#### **linux 安装**

```shell
sudo apt-get install php-ssh2

# 或者

sudo apt-get install php-ssh2
```

```
    
```

然后，在php.ini文件中添加以下行：

```shell
extension=ssh2.so
```

```
    
```

重启你的web服务器，例如Apache或Nginx。

1. 在代码中引入正确的命名空间。在你的控制器文件（例如：YourController.php）中，添加以下代码：

```php
use PHPSSH2\PHPSSH2;
```

现在，你应该可以在你的控制器中使用ssh2_connect()函数了。

#### [**windows安装**](https://www.xp.cn/b.php/77589.html)

> 在Windows上安装php-ssh2扩展的步骤如下：
>
> 1. **下载php-ssh2扩展**：访问php-ssh2扩展的官方下载页面，选择与您的PHP版本相匹配的文件进行下载。如果您的PHP是线程安全的，那么应该选择带有“ts”标记的文件。同时，根据您的系统是32位还是64位，选择相应的“x86”或“x64”版本。
> 2. **解压下载的文件**：解压下载的zip文件，您将得到`libssh2.dll`、`php_ssh.dll`和`php_ssh2.pdb`三个文件。
> 3. **复制文件到相应目录**：
>
> - 将`php_ssh.dll`和`php_ssh2.pdb`文件复制到您的PHP扩展目录，通常是`php/ext/`目录下。
> - 将`libssh2.dll`文件复制到`C:/windows/system32`（对于32位操作系统）或`C:/windows/syswow64`（对于64位操作系统）目录下。确保在这两个目录中都有该文件的副本。
>
> 1. **配置php.ini文件**：打开您的`php.ini`文件，通常位于PHP安装目录下，添加以下行：

```shell
extension=php_ssh.dll
```

> 1. **重启Web服务器**：修改完成后，重启您的Web服务器，例如Apache或Nginx，以使更改生效。
> 2. **验证安装**：创建一个包含`phpinfo()`函数的PHP文件，通过Web浏览器访问该文件，检查输出的信息中是否包含ssh2扩展的相关信息，以确认扩展已成功安装。

**下载扩展安装**

> https://windows.php.net/downloads/pecl/releases/ssh2/1.3.1/
>
> https://windows.php.net/downloads/pecl/releases/ssh2/1.3.1/php_ssh2-1.3.1-7.4-nts-vc15-x64.zip
>
> 安装六十四版本