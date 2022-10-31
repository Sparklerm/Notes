1. 第一步：查看一下系统挂载信息

   ```shell
   [root@localhost ~]# df -h
   Filesystem      Size  Used Avail Use% Mounted on
   /dev/root       2.0G  1.4G  622M  69% /
   devtmpfs        3.8G     0  3.8G   0% /dev
   tmpfs           3.9G     0  3.9G   0% /dev/shm
   tmpfs           3.9G   17M  3.9G   1% /run
   tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
   /dev/mmcblk0p1  286M   54M  233M  19% /boot
   tmpfs           782M     0  782M   0% /run/user/0
   ```

2. 查看一下磁盘分区情况，和总容量

   ```shell
   [root@localhost ~]# fdisk -l
   Disk /dev/mmcblk0: 64.1 GB, 64088965120 bytes, 125173760 sectors
   Units = sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk label type: dos
   Disk identifier: 0x00024e4f
   
           Device Boot      Start         End      Blocks   Id  System
   /dev/mmcblk0p1   *        8192      593919      292864    c  W95 FAT32 (LBA)
   /dev/mmcblk0p2          593920     1593343      499712   82  Linux swap / Solaris
   /dev/mmcblk0p3         1593344     5890047     2148352   83  Linux
   ```

3. 扩展容量，使用的命令是 rootfs-expand

   ```shell
   [root@localhost ~]# which rootfs-expand
   /usr/bin/rootfs-expand
   /dev/mmcblk0p3 /dev/mmcblk0 3
   Extending partition 3 to max size ....
   CHANGED: partition=3 start=1593344 old: size=4296704 end=5890048 new: size=123580383 end=125173727
   Resizing ext4 filesystem ...
   resize2fs 1.42.9 (28-Dec-2013)
   Filesystem at /dev/mmcblk0p3 is mounted on /; on-line resizing required
   old_desc_blocks = 1, new_desc_blocks = 8
   The filesystem on /dev/mmcblk0p3 is now 15447547 blocks long.
   
   Done.
   ```

4. 再次查看磁盘分区情况和挂载信息

   ```shell
   [root@localhost ~]# fdisk -l
   Disk /dev/mmcblk0: 64.1 GB, 64088965120 bytes, 125173760 sectors
   Units = sectors of 1 * 512 = 512 bytes
   Sector size (logical/physical): 512 bytes / 512 bytes
   I/O size (minimum/optimal): 512 bytes / 512 bytes
   Disk label type: dos
   Disk identifier: 0x00024e4f
   
           Device Boot      Start         End      Blocks   Id  System
   /dev/mmcblk0p1   *        8192      593919      292864    c  W95 FAT32 (LBA)
   /dev/mmcblk0p2          593920     1593343      499712   82  Linux swap / Solaris
   /dev/mmcblk0p3         1593344   125173726    61790191+  83  Linux
   [root@localhost ~]# df -h
   Filesystem      Size  Used Avail Use% Mounted on
   /dev/root        58G  1.4G   57G   3% /
   devtmpfs        3.8G     0  3.8G   0% /dev
   tmpfs           3.9G     0  3.9G   0% /dev/shm
   tmpfs           3.9G   17M  3.9G   1% /run
   tmpfs           3.9G     0  3.9G   0% /sys/fs/cgroup
   /dev/mmcblk0p1  286M   54M  233M  19% /boot
   tmpfs           782M     0  782M   0% /run/user/0
   ```

   