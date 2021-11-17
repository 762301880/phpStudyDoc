# 说明

> 源码编译的php发现没有openssl扩展不想重新编译感觉很麻烦

## 参考

| 名称       | 地址                                                         |
| ---------- | ------------------------------------------------------------ |
| 第三方博客 | [link](https://blog.csdn.net/weixin_42392367/article/details/115564198) |
|            |                                                              |

# 方案

## 解压对应php版本的源码包

> 进入到想要的扩展目录

```shell
cd /root/php-7.4.3/ext/openssl

[root@101fe8e80378 openssl]# /usr/local/php7.4.3/bin/phpize
Cannot find config.m4. 
Make sure that you run '/usr/local/php7.4.3/bin/phpize' in the top level source directory of the module
# 复制一份
[root@101fe8e80378 openssl]# cp config0.m4 config.m4
# 执行编译
[root@101fe8e80378 openssl]# /usr/local/php7.4.3/bin/phpize
Configuring for:
PHP Api Version:         20190902
Zend Module Api No:      20190902
Zend Extension Api No:   320190902
# 执行编译
[root@101fe8e80378 openssl]# ./configure --with-openssl --with-php-config=/usr/local/php7.4.3/bin/php-config 
[root@101fe8e80378 openssl]# make && make install

# 再php.ini中添加扩展即可-此时虽然可以看见扩展但是并没有生效
extension=openssl.so

# 最后需要重启服务器才能生效
```

