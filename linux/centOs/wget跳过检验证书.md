#  说明

> wget 下载包的时候有时候会出现校验证书的情况如下
>
> 错误:'****'的证书不受信任。
>
> 错误:'****'的证书没有已知的颁发者。

```shell
ERROR: The certificate of '****' is not trusted.
ERROR: The certificate of '****' hasn't got a known issuer.
```

# 解决方案

> 在结尾后加入   --no-check-certificate 不检查证书 即可下载

```shell
wget https://dev.mysql.com/get/mysql-apt-config_0.8.20-1_all.deb    --no-check-certificate
```

