# 安装报错

## 1. libaio.so.1

```shell
yum install -y libaio
```

## 2. libatomic.so.1

```shell
yum install -y libatomic
```

## 3. ./bin/mysqld: /lib64/libstdc++.so.6: version `GLIBCXX_3.4.21' not found (required by ./bin/mysqld)

> 原因是升级gcc时，生成的动态库没有替换老版本gcc的动态库导致的，将gcc最新版本的动态库替换系统中老版本的动态库即可解决。

1. 检查动态库

   ```shell
   [root@localhost ~]# strings /usr/lib64/libstdc++.so.6 | grep GLIBC
   GLIBCXX_3.4
   GLIBCXX_3.4.1
   GLIBCXX_3.4.2
   GLIBCXX_3.4.3
   GLIBCXX_3.4.4
   GLIBCXX_3.4.5
   GLIBCXX_3.4.6
   GLIBCXX_3.4.7
   GLIBCXX_3.4.8
   GLIBCXX_3.4.9
   GLIBCXX_3.4.10
   GLIBCXX_3.4.11
   GLIBCXX_3.4.12
   GLIBCXX_3.4.13
   GLIBCXX_3.4.14
   GLIBCXX_3.4.15
   GLIBCXX_3.4.16
   GLIBCXX_3.4.17
   GLIBCXX_3.4.18
   GLIBCXX_3.4.19
   GLIBC_2.3
   GLIBC_2.2.5
   GLIBC_2.14
   GLIBC_2.4
   GLIBC_2.3.2
   GLIBCXX_DEBUG_MESSAGE_LENGTH
   ```

2. 解决问题

   1. 查找编译gcc时生成的最新动态库：

      ```
      [root@chengyu ~]# find / -name "libstdc++.so*"
      /usr/lib/libstdc++.so.6
      /usr/lib/libstdc++.so.6.0.19
      /usr/lib/libstdc++.so.5
      /usr/lib/libstdc++.so.5.0.7
      /usr/lib64/libstdc++.so.6
      /usr/lib64/libstdc++.so.6.0.19
      /usr/lib64/libstdc++.so.5
      /usr/lib64/libstdc++.so.5.0.7
      /usr/local/gcc-9.2.0/lib64/libstdc++.so.6.0.27
      /usr/local/gcc-9.2.0/lib64/libstdc++.so.6
      /usr/local/gcc-9.2.0/lib64/libstdc++.so
      /usr/local/gcc-9.2.0/lib64/libstdc++.so.6.0.27-gdb.py
      ……
      ```

   2. 将最新动态库libstdc++.so.6.0.xx复制到/usr/lib64路径下：

      ```
      [root@chengyu ~]# cp /path/libstdc++.so.6.0.xx /usr/lib64
      ```

   3. 重建默认库的软连接：

      ```
      [root@chengyu ~]# cd /usr/lib64
      [root@chengyu lib64]# ll
      [root@chengyu lib64]# mv libstdc++.so.6 libstdc++.so.6.backup
      [root@chengyu lib64]# ln -s libstdc++.so.6.0.xx libstdc++.so.6
      ```

   4. 检查动态库升级完情况：

      ```shell
      [root@chengyu lib64]# strings /usr/lib64/libstdc++.so.6 | grep GLIBC
      GLIBCXX_3.4
      GLIBCXX_3.4.1
      GLIBCXX_3.4.2
      GLIBCXX_3.4.3
      GLIBCXX_3.4.4
      GLIBCXX_3.4.5
      GLIBCXX_3.4.6
      GLIBCXX_3.4.7
      GLIBCXX_3.4.8
      GLIBCXX_3.4.9
      GLIBCXX_3.4.10
      GLIBCXX_3.4.11
      GLIBCXX_3.4.12
      GLIBCXX_3.4.13
      GLIBCXX_3.4.14
      GLIBCXX_3.4.15
      GLIBCXX_3.4.16
      GLIBCXX_3.4.17
      GLIBCXX_3.4.18
      GLIBCXX_3.4.19
      GLIBCXX_3.4.20
      GLIBCXX_3.4.21
      GLIBCXX_3.4.22
      GLIBCXX_3.4.23
      GLIBCXX_3.4.24
      GLIBCXX_3.4.25
      GLIBCXX_3.4.26
      GLIBCXX_3.4.27
      ......
      ```

3. 重新执行MySQL安装命令

