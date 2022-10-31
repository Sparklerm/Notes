1. 通过ifconfig获取网卡信息

   ```shell
   [root@localhost ~]# ifconfig
   eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
           inet 192.168.0.87  netmask 255.255.255.0  broadcast 192.168.0.255
           inet6 fe80::dea6:32ff:febe:7669  prefixlen 64  scopeid 0x20<link>
           ether dc:a6:32:be:76:69  txqueuelen 1000  (Ethernet)
           RX packets 2555  bytes 174260 (170.1 KiB)
           RX errors 0  dropped 46  overruns 0  frame 0
           TX packets 195  bytes 16980 (16.5 KiB)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   
   lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
           inet 127.0.0.1  netmask 255.0.0.0
           inet6 ::1  prefixlen 128  scopeid 0x10<host>
           loop  txqueuelen 1000  (Local Loopback)
           RX packets 106  bytes 9654 (9.4 KiB)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 106  bytes 9654 (9.4 KiB)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   
   wlan0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
           ether 26:3e:26:2d:0d:a8  txqueuelen 1000  (Ethernet)
           RX packets 0  bytes 0 (0.0 B)
           RX errors 0  dropped 0  overruns 0  frame 0
           TX packets 0  bytes 0 (0.0 B)
           TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
   # ether dc:a6:32:be:76:69 为网卡物理地址
   ```

2. 根据网卡新建配置文件：如eth0网卡

   ```shell
   # 在网络配置文件目录新建一个ifcfg-eth0
   cd /etc/sysconfig/network-scripts/
    
   touch ifcfg-eth0
   vi ifcfg-eth0
   ```

   添加以下内容

   ```shell
   # 驱动的网卡型号
   DEVICE=eth0
   # 网络类型（静态还是自动获取）
   BOOTPROTO=static
   # 广播地址
   BROADCAST=192.168.0.255
   # 网卡物理地址
   HWADDR=b8:27:5a:df:46:66
   # 静态ip
   IPADDR=192.168.0.2
   # 不使用ipv6
   IPV6INIT=no
   # 自动配置ipv6关闭
   IPV6_AUTOCONF=no
   # 子网掩码
   NETMASK=255.255.255.0
   # 网关
   GATEWAY=192.168.0.1
   # dns解析地址
   DNS1=114.114.114.114
   # 开机启动
   ONBOOT=yes
   ```

3. 重启网卡载入配置文件

   ```shell
   service network restart
   ```

   