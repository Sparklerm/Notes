# CentOS 7 挂载数据盘

## 查看当前硬盘信息

> Disk /dev/sdb 这个盘是我们要挂载的数据盘，目前为未挂载的状态

```shell
[root@MySQL ~]# fdisk -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a1c48

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

Disk /dev/sdb: 85.9 GB, 85899345920 bytes, 167772160 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-root: 18.2 GB, 18249416704 bytes, 35643392 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

## 硬盘初始化

> fdisk [硬盘]，开始给指定硬盘分区

```shell
[root@MySQL ~]# fdisk /dev/sdb
Welcome to fdisk (util-linux 2.23.2).

Changes will remain in memory only, until you decide to write them.
Be careful before using the write command.

Device does not contain a recognized partition table
Building a new DOS disklabel with disk identifier 0xf250764a.

Command (m for help): n # 输入n开始分区
Partition type:
   p   primary (0 primary, 0 extended, 4 free)
   e   extended
Select (default p): p # 输入p，p为主分区
Partition number (1-4, default 1): 1 # 分区数
First sector (2048-167772159, default 2048): # 默认
Using default value 2048
Last sector, +sectors or +size{K,M,G} (2048-167772159, default 167772159): # 默认
Using default value 167772159
Partition 1 of type Linux and of size 80 GiB is set

Command (m for help): w # 输入w保存
The partition table has been altered!

Calling ioctl() to re-read partition table.
Syncing disks.
```

> 输入fdisk -l查看硬盘信息，可以看到/dev/sdb已初始化，并有了/dev/sdb1分区

```shell
[root@MySQL ~]# fdisk -l

Disk /dev/sda: 21.5 GB, 21474836480 bytes, 41943040 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0x000a1c48

   Device Boot      Start         End      Blocks   Id  System
/dev/sda1   *        2048     2099199     1048576   83  Linux
/dev/sda2         2099200    41943039    19921920   8e  Linux LVM

Disk /dev/sdb: 85.9 GB, 85899345920 bytes, 167772160 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
Disk label type: dos
Disk identifier: 0xf250764a

   Device Boot      Start         End      Blocks   Id  System
/dev/sdb1            2048   167772159    83885056   83  Linux

Disk /dev/mapper/centos-root: 18.2 GB, 18249416704 bytes, 35643392 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes


Disk /dev/mapper/centos-swap: 2147 MB, 2147483648 bytes, 4194304 sectors
Units = sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```

## 挂载分区

> 1. 创建文件夹
>    mkdir /data
>
> 2. 挂载分区到文件夹
>    mount /dev/sdb1 /data
> 3. 设置开机挂载
>    echo /dev/sdb1 /data ext4 defaults 0 0 >> /etc/fstab
> 4. 查看分区挂载信息
>    df -h

```shell
[root@MySQL /]# mkdir /data
[root@MySQL /]# mount /dev/sdb1 /data
[root@MySQL /]# echo /dev/sdb1 /data ext4 defaults 0 0 >> /etc/fstab
[root@MySQL /]# df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G     0  1.9G   0% /dev/shm
tmpfs                    1.9G  8.9M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root   17G  1.8G   16G  11% /
/dev/sda1               1014M  150M  865M  15% /boot
tmpfs                    379M     0  379M   0% /run/user/0
/dev/sdb1                 79G   57M   75G   1% /data

```

