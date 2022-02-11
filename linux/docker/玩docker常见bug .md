  

# [删除容器内部删除映射文件报错](https://blog.csdn.net/qq_33801641/article/details/109571834?spm=1001.2101.3001.6650.9&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-9.pc_relevant_aa&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-9.pc_relevant_aa&utm_relevant_index=13)

**报错示例**

```shell
bash-5.0# cd /data/work/
bash-5.0# ls
laravel_study
bash-5.0# rm -rf laravel_study/
rm: can't remove 'laravel_study': Resource busy
```

**情况描述**

> **docker run -v /data/work/laravel:/data/work/laravel -p 9501:9501 -p 1997:80 -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole**
>
> 以上是我启动后-v 不小心指定的映射目录问题是 我只想在work下当作工作目录 看着下一级目录有点烦
>
> 原因是 -v 指定的目录不可以删除 

 **解决方案 放弃这个容器直接重新指定映射目录** 

> 删除原有容器后重新指定映射目录
>
> **docker run -v /data/work/:/data/work/  -p 9501:9501 -p 1997:80 -itd --entrypoint /bin/sh hyperf/hyperf:7.4-alpine-v3.11-swoole**



