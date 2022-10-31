# 1.安装centos7 镜像

```bash
# 1.搜索并拉取centos镜像(默认最新镜像)
docker search centos
docker pull centos

# 2.建立本机对应docker-centos76 镜像端口映射
# ssh默认的端口为22,我们将docker中centos的22端口映射到宿主机的5000端口
docker run -d -p 5022:22 --name centos76-javabase --privileged=true centos:latest /usr/sbin/init

# 3.进入容器
docker exec -it centos76-javabase /bin/bash
```

#  2.安装常用工具

```bash
# 1.安装常用工具
yum install -y openssh-server vim lrzsz wget gcc-c++ pcre pcre-devel zlib zlib-devel ruby openssl openssl-devel patch bash-completion zlib.i686 libstdc++.i686 lsof unzip zip

# 2.service安装
yum install initscripts 

# 3.ifconfig安装
yum install net-tools.x86_64

# 4.ssh安装(10.0.75.1为docker的ip)
sshd rpm -qa | grep ssh
yum install openssh-server 
service sshd restart
#查看是否启动22端口 
netstat -antp | grep sshd
```

# 3.开启docker-centos76 ssh远程连接

> 如果是云服务器需要开放防火墙端口或添加安全组，如果本地开启防火墙也需要开放端口或关闭防火墙。

```bash
# 1.修改sshd_config 为密码登录，打开注释 PermitRootLogin yes, 允许密码登录,保存退出
vim /etc/ssh/sshd_config

# 2.设置root用户密码
passwd root

# 3.换个服务器远程登录
ssh root@ip -p port
```

