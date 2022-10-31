1. 查看防火墙状态：（active (running) 即是开启状态）

   ```shell
   [root@localhost ~]# systemctl status firewalld
   ● firewalld.service - firewalld - dynamic firewall daemon
      Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled; vendor preset: enabled)
      Active: active (running) since Sun 2022-10-09 06:57:04 UTC; 6s ago
        Docs: man:firewalld(1)
    Main PID: 25661 (firewalld)
      CGroup: /system.slice/firewalld.service
              └─25661 /usr/bin/python2 -Es /usr/sbin/firewalld --nofork --nopid
   
   Oct 09 06:57:03 localhost.localdomain systemd[1]: Starting firewalld - dynamic firewall daemon...
   Oct 09 06:57:04 localhost.localdomain systemd[1]: Started firewalld - dynamic firewall daemon.
   Oct 09 06:57:04 localhost.localdomain firewalld[25661]: WARNING: AllowZoneDrifting is enabled. This is considered an insecure configuration option. It will be removed in a future release. Please consider disabling it now.
   [root@localhost ~]#
   
   ```

2. 查看已开放端口（如22是已开放端口）

   ```shell
   [root@localhost ~]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: eth0
     sources:
     services: dhcpv6-client ssh
     ports: 22/tcp
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   
   [root@localhost ~]#
   ```

3. 防火墙开放端口：（开放端口后需重载防火墙）

   > 命令含义：
   > –zone #作用域
   > –add-port=80/tcp #添加端口，格式为：端口/通讯协议
   > –permanent #永久生效，没有此参数重启后失效
   > firewall-cmd --reload 并不中断用户连接，即不丢失状态信息

   ```shell
   [root@localhost ~]# firewall-cmd --zone=public --add-port=80/tcp --permanent
   success
   [root@localhost ~]# firewall-cmd --reload
   success
   [root@localhost ~]# firewall-cmd --list-all
   public (active)
     target: default
     icmp-block-inversion: no
     interfaces: eth0
     sources:
     services: dhcpv6-client ssh
     ports: 22/tcp 80/tcp
     protocols:
     masquerade: no
     forward-ports:
     source-ports:
     icmp-blocks:
     rich rules:
   
   [root@localhost ~]#
   ```

4. 相关命令

   ```shell
   # firewalld的基本使用
   # 启动
   systemctl start firewalld
   # 关闭
   systemctl stop firewalld
   # 查看状态
   systemctl status firewalld
   # 开机禁用
   systemctl disable firewalld
   # 开机启用
   systemctl enable firewalld
   ```

   > systemctl是CentOS7的服务管理工具中主要的工具，它融合之前service和chkconfig的功能于一体。
   >
   > 启动一个服务：systemctl start firewalld.service
   > 关闭一个服务：systemctl stop firewalld.service
   > 重启一个服务：systemctl restart firewalld.service
   > 显示一个服务的状态：systemctl status firewalld.service
   > 在开机时启用一个服务：systemctl enable firewalld.service
   > 在开机时禁用一个服务：systemctl disable firewalld.service
   > 查看服务是否开机启动：systemctl is-enabled firewalld.service
   > 查看已启动的服务列表：systemctl list-unit-files|grep enabled
   > 查看启动失败的服务列表：systemctl --failed

   > 配置firewalld-cmd
   >
   > 查看版本： firewall-cmd --version
   > 查看帮助： firewall-cmd --help
   > 显示状态： firewall-cmd --state
   > 查看所有打开的端口： firewall-cmd --zone=public --list-ports
   > 更新防火墙规则： firewall-cmd --reload
   > 查看区域信息: firewall-cmd --get-active-zones
   > 查看指定接口所属区域： firewall-cmd --get-zone-of-interface=eth0
   > 拒绝所有包：firewall-cmd --panic-on
   > 取消拒绝状态： firewall-cmd --panic-off
   > 查看是否拒绝： firewall-cmd --query-panic