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

> 安装参考
>
> https://blog.csdn.net/yule117737767/article/details/125203987

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

### 使用解析

> **错误使用**
>
> > 在你的代码中，问题出现在执行 `cd` 命令和 `git pull` 命令的方式上。`ssh2_exec` 会为每个命令创建一个新的 shell，这意味着你在一个命令里执行 `cd`，然后在下一个命令里执行 `git pull` 时，`git pull` 是在一个新的 shell 中执行的，而不是在你已经改变目录的那个目录里执行的。
> >
> > 解决这个问题的方法有几种，下面是两种常用的方法：
>
> ```shell
>         $server = '';//your_server_ip
>         $port = 22;//you_server_port
>         $username = ''; //your_username
>         $password = ''; //your_password
>         // 连接到远程服务器
>         $connection = ssh2_connect($server, $port);
>         if (!$connection) die('连接到服务器失败');
>         // 使用密码进行认证
>         if (!ssh2_auth_password($connection, $username, $password)) die('登录失败');
> 
>         // 执行命令
>         $command1 = 'cd /data/work/tp5.1_gateway_worker';
>         $stream1 = ssh2_exec($connection, $command1);
>         stream_set_blocking($stream1, true);
>         $output1 = stream_get_contents($stream1);
>         echo "命令1输出：\n$output1\n";
>         fclose($stream1);
> 
>         // 执行第二个命令
>         $command2 = 'git pull';
>         $stream2 = ssh2_exec($connection, $command2);
>         stream_set_blocking($stream2, true);
>         $output2 = stream_get_contents($stream2);
>         echo "命令2输出：\n$output2\n";
>         fclose($stream2);
> 
> 
>         //关闭SSH连接
>         ssh2_disconnect($connection);
> ```

**正确使用**

> ### 方法 1: 在一个命令中使用分号 `;` 连接多个命令
>
> 可以将多个命令连接在一起，使用分号 `;` 分隔。这样这些命令会被当做一个整体来执行，确保它们在同一个 shell 中执行。这里是一个示例：

```shell
        $server = '';//your_server_ip
        $port = 22;//you_server_port
        $username = ''; //your_username
        $password = ''; //your_password
        // 连接到远程服务器
        $connection = ssh2_connect($server, $port);
        if (!$connection) die('连接到服务器失败');
        // 使用密码进行认证
        if (!ssh2_auth_password($connection, $username, $password)) die('登录失败');
        // 合并多个命令使用分号;
        $combinedCommand = 'cd /data/work/tp5.1_gateway_worker; git pull';
        $stream = ssh2_exec($connection, $combinedCommand);
        stream_set_blocking($stream, true);
        $output = stream_get_contents($stream);
        echo "命令输出：\n$output\n";
        fclose($stream);
        //关闭SSH连接
        ssh2_disconnect($connection);
```


在你的代码中，问题出现在执行 `cd` 命令和 `git pull` 命令的方式上。`ssh2_exec` 会为每个命令创建一个新的 shell，这意味着你在一个命令里执行 `cd`，然后在下一个命令里执行 `git pull` 时，`git pull` 是在一个新的 shell 中执行的，而不是在你已经改变目录的那个目录里执行的。

解决这个问题的方法有几种，下面是两种常用的方法：

### 方法 1: 在一个命令中使用分号 `;` 连接多个命令

可以将多个命令连接在一起，使用分号 `;` 分隔。这样这些命令会被当做一个整体来执行，确保它们在同一个 shell 中执行。这里是一个示例：

```
phpCopy code<?php

$server = '60.204.148.255';
$port = 22;
$username = 'root';
$password = 'Yly274325132';

// 连接到远程服务器
$connection = ssh2_connect($server, $port);
if (!$connection) die('连接到服务器失败');

// 使用密码进行认证
if (!ssh2_auth_password($connection, $username, $password)) die('登录失败');

// 合并多个命令使用分号;
$combinedCommand = 'cd /data/work/tp5.1_gateway_worker; git pull';
$stream = ssh2_exec($connection, $combinedCommand);
stream_set_blocking($stream, true);
$output = stream_get_contents($stream);
echo "命令输出：\n$output\n";
fclose($stream);

// 关闭连接
ssh2_disconnect($connection);

?>
```

在这个示例中，我们将 `cd` 和 `git pull` 两个命令使用分号 `;` 连接起来，确保它们在同一个 shell 中执行。

### 方法 2: 使用 `ssh2_shell` 执行多个命令（未执行成功）

另一种方法是使用 `ssh2_shell` 函数，在一个 shell 中交互式地执行多个命令。这样可以更加灵活地控制命令的执行，但相应地需要更多的代码。这里是一个示例：

```shell
<?php

$server = '';
$port = 22;
$username = '';
$password = '';

// 连接到远程服务器
$connection = ssh2_connect($server, $port);
if (!$connection) die('连接到服务器失败');

// 使用密码进行认证
if (!ssh2_auth_password($connection, $username, $password)) die('登录失败');

// 打开一个 shell
$shell = ssh2_shell($connection, 'bash');
if (!$shell) die('无法打开 shell');

// 执行命令
fwrite($shell, "cd /data/work/tp5.1_gateway_worker\n");
fwrite($shell, "git pull\n");

// 等待命令执行完成
while ($line = fgets($shell)) {
    echo $line;
}

// 关闭 shell
fclose($shell);

// 关闭连接
ssh2_disconnect($connection);

?>
```

# 方法二

> 在 PHP 中实现自动部署代码钩子（Automatic Deployment Code Hooks）通常涉及使用版本控制系统（如 Git）和 Web 服务器（如 Apache 或 Nginx）配合。这样，当代码被推送到版本控制系统中时，钩子（Hooks）可以触发自动化脚本来更新部署。
>
> 下面是一个基本的自动部署代码钩子的示例，假设你正在使用 Git 和 Apache 服务器。这个示例假设你的服务器上已经安装了 Git，并且你有权限运行 shell 脚本。

### 步骤

#### 1. 创建部署脚本

首先，你需要在你的项目中创建一个用于自动部署的脚本，比如 `deploy.php`。这个脚本将在每次推送到指定分支时运行。

```php
<?php
// 设置密钥以确保只有你知道的人可以访问该脚本
$secret = 'your_secret_key';

if ($_GET['secret'] !== $secret) {
    header('HTTP/1.0 403 Forbidden');
    die('Access Forbidden.');
}

// 获取最新的代码
exec('git pull');

// 可选：运行 composer install 或其他构建步骤
// exec('composer install');

// 可选：清除缓存或其他必要的步骤
// exec('php artisan cache:clear');

// 输出结果
echo 'Deployment successful.';
```

确保在这个脚本中设置一个安全的密钥，以防止未经授权的访问。在这个例子中，我们将在 URL 中发送这个密钥来触发部署脚本。

#### 2. 配置 Git 钩子

进入你的项目的 `.git` 目录，然后找到或创建 `hooks` 目录。在这个目录中，你可以创建一个 post-receive 钩子，用于在推送后触发部署脚本。

```bash
cd /path/to/your/repo/.git/hooks
```

然后创建一个 `post-receive` 文件：

```bash
nano post-receive
```

编辑这个文件，添加如下内容：

```bash
#!/bin/bash
curl http://yourdomain.com/deploy.php?secret=your_secret_key
```

记得给这个文件执行权限：

```sh
chmod +x post-receive
```

这样，每次有代码被推送到你的 Git 仓库时，Git 会运行这个 `post-receive` 脚本，并且这个脚本会触发部署脚本。

#### 3. 配置 Web 服务器

最后，在你的 Web 服务器配置中，你需要确保请求到 `deploy.php` 的请求被正确地路由到你的脚本。

对于 Apache，你可以在 `.htaccess` 文件中添加类似下面的规则：

```bash
RewriteEngine On
RewriteRule ^deploy.php$ /path/to/deploy.php [L]
```

对于 Nginx，你可以在配置文件中添加类似下面的配置：

```nginx
location /deploy.php {
    alias /path/to/deploy.php;
    # 其他配置...
}
```

### 注意事项

- **安全性**：确保部署脚本和 Git 钩子是安全的。使用一个独特的密钥来验证请求，以防止未经授权的访问。
- **日志记录**：在部署脚本中添加日志记录可以帮助你跟踪部署的历史和可能出现的问题。
- **权限**：确保你的部署脚本有足够的权限来运行 Git 命令和其他必要的操作。

这只是一个基本的示例，实际上你可能需要根据你的项目和服务器的配置进行调整。对于更复杂的部署需求，可能需要使用更专业的部署工具或服务。