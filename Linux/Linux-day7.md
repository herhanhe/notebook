# Linux_day7 使用RAID与LVM磁盘阵列技术

---

- [Linux_day7 使用RAID与LVM磁盘阵列技术](#linuxday7-%e4%bd%bf%e7%94%a8raid%e4%b8%8elvm%e7%a3%81%e7%9b%98%e9%98%b5%e5%88%97%e6%8a%80%e6%9c%af)
  - [1.RAID磁盘冗余阵列](#1raid%e7%a3%81%e7%9b%98%e5%86%97%e4%bd%99%e9%98%b5%e5%88%97)
    - [1.1部署磁盘阵列](#11%e9%83%a8%e7%bd%b2%e7%a3%81%e7%9b%98%e9%98%b5%e5%88%97)
    - [1.2 损坏磁盘阵列及修复](#12-%e6%8d%9f%e5%9d%8f%e7%a3%81%e7%9b%98%e9%98%b5%e5%88%97%e5%8f%8a%e4%bf%ae%e5%a4%8d)
    - [1.3 磁盘阵列+备份盘](#13-%e7%a3%81%e7%9b%98%e9%98%b5%e5%88%97%e5%a4%87%e4%bb%bd%e7%9b%98)
  - [2.LVM逻辑卷管理器](#2lvm%e9%80%bb%e8%be%91%e5%8d%b7%e7%ae%a1%e7%90%86%e5%99%a8)
    - [2.1 部署逻辑卷](#21-%e9%83%a8%e7%bd%b2%e9%80%bb%e8%be%91%e5%8d%b7)
    - [2.2 扩容逻辑卷](#22-%e6%89%a9%e5%ae%b9%e9%80%bb%e8%be%91%e5%8d%b7)
    - [2.3 缩小逻辑卷](#23-%e7%bc%a9%e5%b0%8f%e9%80%bb%e8%be%91%e5%8d%b7)
    - [2.4 逻辑卷快照](#24-%e9%80%bb%e8%be%91%e5%8d%b7%e5%bf%ab%e7%85%a7)
    - [2.5 删除逻辑卷](#25-%e5%88%a0%e9%99%a4%e9%80%bb%e8%be%91%e5%8d%b7)

## 1.RAID磁盘冗余阵列

* `RAID 0`
  * `RAID 0`技术把多块物理硬盘设备（至少两块）通过硬件或软件的方式串联在一起，组成一个大的卷组，并将数据依次写入到各个物理硬盘中。
  * 能够有效地提升硬盘数据的吞吐速度
  * 不具备数据备份和错误修复能力，若任意一块硬盘发生故障将导致整个系统的数据收到破坏
  
    ![7-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586691254613.png)

* `RAID 1`
  * `RAID 1`是把两块以上的硬盘设备进行绑定，在写入数据时，是将数据同时写入到多块硬盘设备上（可以将其视为数据的镜像或备份）。
  * `RAID 1`技术虽然十分注重数据的安全性，但是因为是在多块硬盘设备中写入了相同的数据，因此硬盘设备的利用率得以下降
  * 而且，由于需要把数据同时写入到两块以上的硬盘设备，这无疑也在一定程度上增大了系统计算功能的负载

    ![7-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586697063815.png)

* `RAID 5`
  * `RAID 5`技术是把硬盘设备的数据奇偶校验信息保存到其他硬盘设备中
  * `RAID 5`技术实际上没有备份硬盘中的真实数据信息，而是当硬盘设备出现问题后通过奇偶校验信息来尝试重建损坏的数据
  * `RAID 5`这样的技术特性“妥协”地兼顾了硬盘设备的读写速度、数据安全性与存储成本问题
  
    ![7-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586697432495.png)

* `RAID 10`
  * `RAID 10`技术是`RAID 1`+`RAID 0`技术的一个“组合体”。
  * `RAID 10`技术需要至少4块硬盘来组建，其中先分别两两制作成`RAID 1`磁盘阵列，以保证数据的安全性；然后再对两个`RAID 1`磁盘阵列实施`RAID 0`技术，进一步提高硬盘设备的读写速度。
  
    ![7-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586697695367.png)

### 1.1部署磁盘阵列

* `mdadm`命令
  * 用于管理`Linux`系统中的软件`RAID`硬盘阵列
  * 格式为“`mdadm` [模式] <RAID设备名> [选项] [成员设备名称]”
  * 常用参数：

    参数|作用
    -|-
    `-a`|检测设备名称 yes参数代表自动创建设备文件
    `-n`|指定设备数量
    `-l`|指定RAID级别
    `-C`|创建一个RAID阵列卡
    `-v`|显示创建的过程
    `-f`|模拟设备损坏
    `-r`|移除设备
    `-Q`|查看摘要信息
    `-D`|查看详细信息
    `-S`|停止RAID磁盘阵列
    `-x`|备份盘数量

  * 范例：创建RAID 10方案
    * 1.使用`mdadm`命令创建`RAID 10`

      ```shell
        [root@localhost Desktop]# mdadm -Cv /dev/md0 -a yes -n 4 -l 10 /dev/sdc /dev/sdd /dev/sde /dev/sdf
        mdadm: layout defaults to n2
        mdadm: layout defaults to n2
        mdadm: chunk size defaults to 512K
        mdadm: size set to 5238272K
        mdadm: Defaulting to version 1.2 metadata
        mdadm: array /dev/md0 started.
      ```

    * 2.把制作好的`RAID`磁盘阵列格式化为`xfs`格式

      ```shell
        [root@localhost Desktop]# mkfs.xfs /dev/md0
        log stripe unit (524288 bytes) is too large (maximum is 256KiB)
        log stripe unit adjusted to 32KiB
        meta-data=/dev/md0        isize=256    agcount=16, agsize=163712 blks
                 =                sectsz=512   attr=2, projid32bit=1
                 =                crc=0
        data     =                bsize=4096   blocks=2619136, imaxpct=25
                 =                sunit=128    swidth=256 blks
        naming   =version 2       bsize=4096   ascii-ci=0 ftype=0
        log      =internal log    bsize=4096   blocks=2560, version=2
                 =                sectsz=512   sunit=8 blks, lazy-count=1
        realtime =none            extsz=4096   blocks=0, rtextents=0
      ```

    * 3.创建挂载点然后把硬盘设备进行挂载操作

      ```shell
        [root@localhost Desktop]# mkdir /RAID
        [root@localhost Desktop]# mount /dev/md0 /RAID
        [root@localhost Desktop]# df -h
        Filesystem             Size  Used Avail Use% Mounted on
        /dev/mapper/rhel-root   18G  2.9G   15G  17% /
        devtmpfs               985M     0  985M   0% /dev
        tmpfs                  994M  140K  994M   1% /dev/shm
        tmpfs                  994M  8.8M  986M   1% /run
        tmpfs                  994M     0  994M   0% /sys/fs/cgroup
        /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
        /dev/sdb1              2.0G   66M  2.0G   4% /newFS
        /dev/sda1              497M  127M  371M  26% /boot
        /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
        /dev/md0                10G   33M   10G   1% /RAID
      ```

    * 4.查看/dev/md0磁盘阵列的详细信息，并把挂载信息写入到配置文件中，使其永久生效

      ```shell
        [root@localhost Desktop]# mdadm -D /dev/md0
        /dev/md0:
                Version : 1.2
          Creation Time : Sun Apr 12 21:52:23 2020
             Raid Level : raid10
             Array Size : 10476544 (9.99 GiB 10.73 GB)
          Used Dev Size : 5238272 (5.00 GiB 5.36 GB)
           Raid Devices : 4
          Total Devices : 4
            Persistence : Superblock is persistent

            Update Time : Sun Apr 12 21:53:55 2020
                  State : clean
         Active Devices : 4
        Working Devices : 4
         Failed Devices : 0
          Spare Devices : 0

                 Layout : near=2
             Chunk Size : 512K

                   Name : localhost.localdomain:0  (local to host localhost.localdomain)
                   UUID : d6707e47:20c6c8c5:fc20d688:1c998ef9
                 Events : 19

            Number   Major   Minor   RaidDevice State
               0       8       32        0      active sync   /dev/sdc
               1       8       48        1      active sync   /dev/sdd
               2       8       64        2      active sync   /dev/sde
               3       8       80        3      active sync   /dev/sdf
        [root@localhost Desktop]# echo "/dev/md0 /RAID xfs defaults 0 0" >> /etc/fstab
      ```

### 1.2 损坏磁盘阵列及修复

* 范例：
  * 1.损坏磁盘阵列

    ```shell
      [root@localhost Desktop]# mdadm /dev/md0 -f /dev/sdd
      mdadm: set /dev/sdd faulty in /dev/md0
      [root@localhost Desktop]# mdadm -D /dev/md0
      /dev/md0:
              Version : 1.2
        Creation Time : Sun Apr 12 21:52:23 2020
           Raid Level : raid10
           Array Size : 10476544 (9.99 GiB 10.73 GB)
        Used Dev Size : 5238272 (5.00 GiB 5.36 GB)
         Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Sun Apr 12 23:13:40 2020
                State : clean, degraded
       Active Devices : 3
      Working Devices : 3
       Failed Devices : 1
        Spare Devices : 0

               Layout : near=2
           Chunk Size : 512K

                 Name : localhost.localdomain:0  (local to host localhost.localdomain)
                 UUID : d6707e47:20c6c8c5:fc20d688:1c998ef9
               Events : 54

          Number   Major   Minor   RaidDevice State
             0       8       32        0      active sync   /dev/sdc
             1       0        0        1      removed
             2       8       64        2      active sync   /dev/sde
             3       8       80        3      active sync   /dev/sdf

             4       8       48        -      faulty   /dev/sdd
    ```

  * 2.磁盘阵列修复

    ```shell
      [root@localhost Desktop]# reboot
      [root@localhost Desktop]# umount /RAID
      [root@localhost Desktop]# mdadm /dev/md0 -a /dev/sdd
      mdadm: added /dev/sdd
      [root@localhost Desktop]# mdadm -D /dev/md0
      /dev/md0:
              Version : 1.2
        Creation Time : Sun Apr 12 21:52:23 2020
           Raid Level : raid10
           Array Size : 10476544 (9.99 GiB 10.73 GB)
        Used Dev Size : 5238272 (5.00 GiB 5.36 GB)
         Raid Devices : 4
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Sun Apr 12 23:29:41 2020
                State : clean, degraded, recovering 
       Active Devices : 3
      Working Devices : 4
       Failed Devices : 0
        Spare Devices : 1

               Layout : near=2
           Chunk Size : 512K

       Rebuild Status : 83% complete

                 Name : localhost.localdomain:0  (local to host localhost.localdomain)
                 UUID : d6707e47:20c6c8c5:fc20d688:1c998ef9
               Events : 77

          Number   Major   Minor   RaidDevice State
             0       8       32        0      active sync   /dev/sdc
             4       8       48        1      spare rebuilding   /dev/sdd
             2       8       64        2      active sync   /dev/sde
             3       8       80        3      active sync   /dev/sdf
      [root@localhost Desktop]# mount -a
    ```

### 1.3 磁盘阵列+备份盘

* 范例：
  * 1.创建磁盘阵列+备份盘

    ```shell
      [root@localhost Desktop]# umount /dev/md0
      [root@localhost Desktop]# mdadm -S /dev/md0
      mdadm: stopped /dev/md0
      [root@localhost Desktop]# mdadm -D /dev/md0
      mdadm: cannot open /dev/md0: No such file or directory
      [root@localhost Desktop]# mdadm -Cv /dev/md0 -n 3 -l 5 -x 1 /dev/sdc /dev/sdd /dev/sde /dev/sdf
      mdadm: layout defaults to left-symmetric
      mdadm: layout defaults to left-symmetric
      mdadm: chunk size defaults to 512K
      mdadm: /dev/sdc appears to be part of a raid array:
          level=raid10 devices=4 ctime=Sun Apr 12 21:52:23 2020
      mdadm: /dev/sdd appears to be part of a raid array:
          level=raid10 devices=4 ctime=Sun Apr 12 21:52:23 2020
      mdadm: /dev/sde appears to be part of a raid array:
          level=raid10 devices=4 ctime=Sun Apr 12 21:52:23 2020
      mdadm: /dev/sdf appears to be part of a raid array:
          level=raid10 devices=4 ctime=Sun Apr 12 21:52:23 2020
      mdadm: size set to 5238272K
      Continue creating array? y
      mdadm: Defaulting to version 1.2 metadata
      mdadm: array /dev/md0 started.
      [root@localhost Desktop]# mdadm -D /dev/md0
      /dev/md0:
              Version : 1.2
        Creation Time : Sun Apr 12 23:42:30 2020
           Raid Level : raid5
           Array Size : 10476544 (9.99 GiB 10.73 GB)
        Used Dev Size : 5238272 (5.00 GiB 5.36 GB)
         Raid Devices : 3
        Total Devices : 4
          Persistence : Superblock is persistent

          Update Time : Sun Apr 12 23:42:44 2020
                State : clean, degraded, recovering 
       Active Devices : 2
      Working Devices : 4
       Failed Devices : 0
        Spare Devices : 2

               Layout : left-symmetric
           Chunk Size : 512K

       Rebuild Status : 51% complete

                 Name : localhost.localdomain:0  (local to host localhost.localdomain)
                 UUID : c0f54fc7:bbf70d6c:e74a04a2:b95a9cdb
               Events : 9

          Number   Major   Minor   RaidDevice State
             0       8       32        0      active sync   /dev/sdc
             1       8       48        1      active sync   /dev/sdd
             4       8       64        2      spare rebuilding   /dev/sde

             3       8       80        -      spare   /dev/sdf
    ```

  * 2.格式化和挂载

    ```shell
      [root@localhost Desktop]# mkfs.xfs /dev/md0
      mkfs.xfs: /dev/md0 appears to contain an existing filesystem (xfs).
      mkfs.xfs: Use the -f option to force overwrite.
      [root@localhost Desktop]# mkfs.xfs -f /dev/md0
      log stripe unit (524288 bytes) is too large (maximum is 256KiB)
      log stripe unit adjusted to 32KiB
      meta-data=/dev/md0        isize=256    agcount=16, agsize=163712 blks
               =                sectsz=512   attr=2, projid32bit=1
               =                crc=0
      data     =                bsize=4096   blocks=2619136, imaxpct=25
               =                sunit=128    swidth=256 blks
      naming   =version 2       bsize=4096   ascii-ci=0 ftype=0
      log      =internal log    bsize=4096   blocks=2560, version=2
               =                sectsz=512   sunit=8 blks, lazy-count=1
      realtime =none              extsz=4096   blocks=0, rtextents=0
      [root@localhost Desktop]# mount /dev/md0 /RAID/
      [root@localhost Desktop]# df -h
      Filesystem             Size  Used Avail Use% Mounted on
      /dev/mapper/rhel-root   18G  2.9G   15G  17% /
      devtmpfs               985M     0  985M   0% /dev
      tmpfs                  994M  140K  994M   1% /dev/shm
      tmpfs                  994M  8.9M  986M   1% /run
      tmpfs                  994M     0  994M   0% /sys/fs/cgroup
      /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
      /dev/sdb1              2.0G   66M  2.0G   4% /newFS
      /dev/sda1              497M  127M  371M  26% /boot
      /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
      /dev/md0                10G   33M   10G   1% /RAID
    ```

  * 3.损坏磁盘阵列，备份盘后补

    ```shell
      [root@localhost Desktop]# mdadm /dev/md0 -f /dev/sdd
      mdadm: set /dev/sdd faulty in /dev/md0
      [root@localhost Desktop]# mdadm -D /dev/md0
      /dev/md0:
               Version : 1.2
         Creation Time : Sun Apr 12 23:42:30 2020
            Raid Level : raid5
            Array Size : 10476544 (9.99 GiB 10.73 GB)
         Used Dev Size : 5238272 (5.00 GiB 5.36 GB)
          Raid Devices : 3
         Total Devices : 4
           Persistence : Superblock is persistent

           Update Time : Sun Apr 12 23:49:48 2020
                 State : clean, degraded, recovering
        Active Devices : 2
       Working Devices : 3
        Failed Devices : 1
         Spare Devices : 1

                Layout : left-symmetric
            Chunk Size : 512K

        Rebuild Status : 42% complete

                  Name : localhost.localdomain:0  (local to host localhost.localdomain)
                  UUID : c0f54fc7:bbf70d6c:e74a04a2:b95a9cdb
                Events : 28

        Number   Major   Minor   RaidDevice State
           0       8       32        0      active sync   /dev/sdc
           3       8       80        1      spare rebuilding   /dev/sdf
           4       8       64        2      active sync   /dev/sde

           1       8       48        -      faulty   /dev/sdd
    ```

## 2.LVM逻辑卷管理器

* 逻辑卷管理器是Linux系统用于对硬盘分区进行管理的一种机制，理论性较强，其创建初衷是为了解决硬盘设备在创建分区后不易修改分区大小的缺陷。
* LVM技术是在硬盘分区和文件系统之间添加了一个逻辑层，它提供了一个抽象的卷组，可以把多块硬盘进行卷组合并。这样一来，用户不必关心物理硬盘设备的底层架构和布局，就可以实现对硬盘分区的动态调整。

    ![7-5](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586782216647.png)

* LVM的核心理念：卷组建立在物理卷之上，一个卷组可以包含多个物理卷，而且在卷组创建之后也可以继续向其中添加新的物理卷。逻辑卷是用卷组中空闲的资源建立的，并且逻辑卷在建立后可以动态地扩展或缩小空间。

### 2.1 部署逻辑卷

* 部署LVM时，需要逐个配置物理卷、卷组、和逻辑卷。
* 常用的LVM部署命令

    功能/命令|物理卷管理|卷组管理|逻辑卷管理
    -|-|-|-
    扫描|`pvscan`|`vgscan`|`lvscan`
    建立|`pvcreate`|`vgcreate`|`lvcreate`
    显示|`pvdisplay`|`vgdisplay`|`lvdisplay`
    删除|`pvremove`|`vgremove`|`lvremove`
    扩展||`vgextend`|`lvextend`
    缩小||`vgreduce`|`lvreduce`

* 范例：部署逻辑卷步奏：
  * 1.让新添加的两块硬盘设备支持LVN技术。

    ```shell
      [root@localhost Desktop]# pvcreate /dev/sdc /dev/sdd /dev/sde /dev/sdf
      Physical volume "/dev/sdc" successfully created  
      Physical volume "/dev/sdd" successfully created  
      Physical volume "/dev/sde" successfully created
      Physical volume "/dev/sdf" successfully created
    ```

  * 2.把四块硬盘设备加入到`herhan`卷组，然后查看卷组的状态

    ```shell
      [root@localhost Desktop]# vgcreate herhan /dev/sdc /dev/sdd /dev/sde /dev/sdf
       Volume group "herhan" successfully created
      [root@localhost Desktop]# vgdisplay
      --- Volume group ---
       VG Name               herhan
       System ID
       Format                lvm2
       Metadata Areas        4
       Metadata Sequence No  1
       VG Access             read/write
       VG Status             resizable
       MAX LV                0
       Cur LV                0
       Open LV               0
       Max PV                0
       Cur PV                4
       Act PV                4
       VG Size               19.98 GiB
       PE Size               4.00 MiB
       Total PE              5116
       Alloc PE / Size       0 / 0
       Free  PE / Size       5116 / 19.98 GiB
       VG UUID               ul133W-VC5d-hpmT-oJp2-a2Ki-1cYo-trZ6kD
    ```

  * 3.切割出一个约为200MB的逻辑卷设备
    * 方法1是以容器为单位，所使用的参数为`-L`。例如，使用`-L 150M`生成一个大小为150MB的逻辑卷
    * 方法2是以基础单元的个数为单元，所使用的参数为`-l`。每个基本单元的大小默认为`4MB`。例如，使用`-l 37`可以生成一个大小为`37x4M=148M`的逻辑卷。

    ```shell
      [root@localhost Desktop]# lvcreate -n vo -l 50 herhan
       Logical volume "vo" created
      [root@localhost Desktop]# lvdisplay
      --- Logical volume ---
       LV Path                /dev/herhan/vo
       LV Name                vo
       VG Name                herhan
       LV UUID                32I0Vu-43lL-rFWh-8Hip-1q7W-S4DF-s8uqUI
       LV Write Access        read/write
       LV Creation host, time localhost.localdomain, 2020-04-13 21:35:19 +0800
       LV Status              available
       # open                 0
       LV Size                200.00 MiB
       Current LE             50
       Segments               1
       Allocation             inherit
       Read ahead sectors     auto
       - currently set to     8192
       Block device           253:2
    ```

  * 4.把生成好的逻辑卷进行格式化，然后挂载使用。

    ```shell
      [root@localhost Desktop]# mkfs.ext4 /dev/herhan/vo
      mke2fs 1.42.9 (28-Dec-2013)
      Filesystem label=
      OS type: Linux
      Block size=1024 (log=0)
      Fragment size=1024 (log=0)
      Stride=0 blocks, Stripe width=0 blocks
      51200 inodes, 204800 blocks
      10240 blocks (5.00%) reserved for the super user
      First data block=1
      Maximum filesystem blocks=33816576
      25 block groups
      8192 blocks per group, 8192 fragments per group
      2048 inodes per group
      Superblock backups stored on blocks:
        8193, 24577, 40961, 57345, 73729

      Allocating group tables: done
      Writing inode tables: done
      Creating journal (4096 blocks): done
      Writing superblocks and filesystem accounting information: done

      [root@localhost Desktop]# mkdir /herhan
      [root@localhost Desktop]# mount /dev/herhan/vo /herhan/
    ```

  * 5.查看挂载状态，并写入到配置文件，使其永久生效。

    ```shell
      [root@localhost Desktop]# df -h
      Filesystem             Size  Used Avail Use% Mounted on
      /dev/mapper/rhel-root   18G  2.9G   15G  17% /
      devtmpfs               985M     0  985M   0% /dev
      tmpfs                  994M  140K  994M   1% /dev/shm
      tmpfs                  994M  8.9M  986M   1% /run
      tmpfs                  994M     0  994M   0% /sys/fs/cgroup
      /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
      /dev/sdb1              2.0G   66M  2.0G   4% /newFS
      /dev/sda1              497M  127M  371M  26% /boot
      /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
      /dev/mapper/herhan-vo  190M  1.6M  175M   1% /herhan
      [root@localhost Desktop]# echo "/dev/herhan/vo /herhan ext4 defaults 0 0" >> /etc/fstab
    ```

### 2.2 扩容逻辑卷

* 用户在使用存储设备时感知不到设备底层的架构和布局，更不用关心底层是由多少块硬盘组成的，只要卷组中有足够的资源，就可以一直为逻辑卷扩容。扩展前请一定要记得卸载设备和挂载点的关联。

    ```shell
      [root@localhost Desktop]# umount /herhan
    ```

* 1.把上一个实验中的逻辑卷vo扩展至300MB

    ```shell
      [root@localhost Desktop]# lvextend -L 300M /dev/herhan/vo
      Extending logical volume vo to 300.00 MiB
      Logical volume vo successfully resized
    ```

* 2.检查硬盘完整性，重新挂载硬盘设备，并重置硬盘容量。

    ```shell
      [root@localhost Desktop]# e2fsck -f /dev/herhan/vo
      e2fsck 1.42.9 (28-Dec-2013)
      Pass 1: Checking inodes, blocks, and sizes
      Pass 2: Checking directory structure
      Pass 3: Checking directory connectivity
      Pass 4: Checking reference counts
      Pass 5: Checking group summary information
      /dev/herhan/vo: 11/51200 files (0.0% non-contiguous), 12115/204800 blocks
      [root@localhost Desktop]# mount -a
      [root@localhost Desktop]# df -h
      Filesystem             Size  Used Avail Use% Mounted on
      /dev/mapper/rhel-root   18G  2.9G   15G  17% /
      devtmpfs               985M     0  985M   0% /dev
      tmpfs                  994M  140K  994M   1% /dev/shm
      tmpfs                  994M  8.8M  986M   1% /run
      tmpfs                  994M     0  994M   0% /sys/fs/cgroup
      /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
      /dev/sdb1              2.0G   66M  2.0G   4% /newFS
      /dev/sda1              497M  127M  371M  26% /boot
      /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
      /dev/mapper/herhan-vo  190M  1.6M  175M   1% /herhan
      [root@localhost Desktop]# resize2fs /dev/herhan/vo
      resize2fs 1.42.9 (28-Dec-2013)
      Filesystem at /dev/herhan/vo is mounted on /herhan; on-line resizing required
      old_desc_blocks = 2, new_desc_blocks = 3
      The filesystem on /dev/herhan/vo is now 307200 blocks long.
    ```

* 3.查看挂载状态。

    ```shell
      [root@localhost Desktop]# df -h
      Filesystem             Size  Used Avail Use% Mounted on
      /dev/mapper/rhel-root   18G  2.9G   15G  17% /
      devtmpfs               985M     0  985M   0% /dev
      tmpfs                  994M  140K  994M   1% /dev/shm
      tmpfs                  994M  8.8M  986M   1% /run
      tmpfs                  994M     0  994M   0% /sys/fs/cgroup
      /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
      /dev/sdb1              2.0G   66M  2.0G   4% /newFS
      /dev/sda1              497M  127M  371M  26% /boot
      /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
      /dev/mapper/herhan-vo  287M  2.1M  267M   1% /herhan
    ```

### 2.3 缩小逻辑卷

* 在对LVM逻辑卷进行缩容操作之前，要先检查文件系统的完整性（当然这也是为了保证我们的数据安全）。在执行缩容操作前记得先把文件系统卸载掉。

    ```shell
      [root@localhost Desktop]# umount /herhan
    ```

* 1.检查文件系统的完整性。

    ```shell
      [root@localhost Desktop]# e2fsck -f /dev/herhan/vo
      e2fsck 1.42.9 (28-Dec-2013)
      Pass 1: Checking inodes, blocks, and sizes
      Pass 2: Checking directory structure
      Pass 3: Checking directory connectivity
      Pass 4: Checking reference counts
      Pass 5: Checking group summary information
      /dev/herhan/vo: 11/77824 files (0.0% non-contiguous), 15987/307200 blocks
    ```

* 2.把逻辑卷vo的容量减少到100M

    ```shell  
      [root@localhost Desktop]# resize2fs /dev/herhan/vo 100M
      resize2fs 1.42.9 (28-Dec-2013)
      Resizing the filesystem on /dev/herhan/vo to 102400 (1k) blocks.
      The filesystem on /dev/herhan/vo is now 102400 blocks long.

      [root@localhost Desktop]# lvreduce -L 100M /dev/herhan/vo
      WARNING: Reducing active logical volume to 100.00 MiB
      THIS MAY DESTROY YOUR DATA (filesystem etc.)
      Do you really want to reduce vo? [y/n]: y
      Reducing logical volume vo to 100.00 MiB
      Logical volume vo successfully resized
    ```

* 3.重新挂载文件系统并查看系统状态。

    ```shell
      [root@localhost Desktop]# mount -a
      [root@localhost Desktop]# df -h
      Filesystem             Size  Used Avail Use% Mounted on
      /dev/mapper/rhel-root   18G  2.9G   15G  17% /
      devtmpfs               985M     0  985M   0% /dev
      tmpfs                  994M  140K  994M   1% /dev/shm
      tmpfs                  994M  8.8M  986M   1% /run
      tmpfs                  994M     0  994M   0% /sys/fs/cgroup
      /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
      /dev/sdb1              2.0G   66M  2.0G   4% /newFS
      /dev/sda1              497M  127M  371M  26% /boot
      /dev/sr0               3.5G  3.5G     0 100% /run/media/root/RHEL-7.0 Server.x86_64
      /dev/mapper/herhan-vo   93M  1.6M   85M   2% /herhan
    ```

### 2.4 逻辑卷快照

* `LVM`还具备有“快照卷”功能，该功能类似于虚拟机软件的还原时间点功能。
* `LVM`的快照卷功能有两个特点：
  * 快照卷的容量必须等同于逻辑卷的容量；
  * 快照卷仅一次有效，一旦执行还原操作后则会被立即自动删除。

* 范例：
  * 查看卷组的信息
  * 接下来用重定向往逻辑卷设备所挂载的目录中写入一个文件。

    ```shell
      [root@localhost Desktop]# echo "Welcome to herhan.com" > /herhan/readme.txt
      [root@localhost Desktop]# ls -l /herhan
      total 14
      drwx------. 2 root root 12288 Apr 13 21:44 lost+found
      -rw-r--r--. 1 root root    22 Apr 13 23:16 readme.txt
    ```
  
  * 1.使用`-s`参数生成一个快照卷，使用`-L`参数指定切割的大小。另外，还需要在命令后面写上是针对哪个逻辑卷执行的快照操作。

    ```shell
      [root@localhost Desktop]# lvcreate -L 100M -s -n SNAP /dev/herhan/vo
      Logical volume "SNAP" created
      [root@localhost Desktop]# lvdisplay
       --- Logical volume ---
       LV Path                /dev/herhan/SNAP
       LV Name                SNAP
       VG Name                herhan
       LV UUID                JEVqxT-bc0P-U8of-so2d-dnpv-wxSf-ls8Jvn
       LV Write Access        read/write
       LV Creation host, time localhost.localdomain, 2020-04-13 23:21:13 +0800
       LV snapshot status     active destination for vo
       LV Status              available
       # open                 0
       LV Size                100.00 MiB
       Current LE             25
       COW-table size         100.00 MiB
       COW-table LE           25
       Allocated to snapshot  0.01%
       Snapshot chunk size    4.00 KiB
       Segments               1
       Allocation             inherit
       Read ahead sectors     auto
       - currently set to     8192
       Block device           253:3
    ```

  * 2.在逻辑卷所挂载的目录中创建一个100MB的垃圾文件，然后再查看快照卷的状态。可以发现存储空间占的用量上升了。

    ```shell
      [root@localhost Desktop]# dd if=/dev/zero of=/herhan/files count=1 bs=100M
      dd: error writing ‘/herhan/files’: No space left on device
      1+0 records in
      0+0 records out
      93528064 bytes (94 MB) copied, 0.932516 s, 100 MB/s
    ```

  * 3.为了校验SNAP快照卷的效果，需要对逻辑卷进行快照还原操作。在此之前记得先卸载掉逻辑卷设备与目录的挂载。

    ```shell
      [root@localhost Desktop]# lvconvert --merge /dev/herhan/SNAP 
      Merging of volume SNAP started.
      vo: Merged: 24.8%
      vo: Merged: 100.0%
      Merge of snapshot into logical volume vo has finished.
      Logical volume "SNAP" successfully removed
    ```

  * 4.快照卷会被自动删除掉，并且刚刚在逻辑卷设备被执行快照操作后再创建出来的100MB的垃圾文件也被清除了。

    ```shell
      [root@localhost Desktop]# mount -a
      [root@localhost Desktop]# ls -l /herhan
      total 14
      drwx------. 2 root root 12288 Apr 13 21:44 lost+found
      -rw-r--r--. 1 root root    22 Apr 13 23:16 readme.txt
    ```

### 2.5 删除逻辑卷

* 需要提前备份好重要的数据信息，然后依次删除逻辑卷、卷组、物理卷设备，这个顺序不可颠倒。

* 1.取消逻辑卷与目录的挂载关联，删除配置文件中永久生效的设备参数。

    ```shell
      [root@localhost Desktop]# umount /herhan
      [root@localhost Desktop]# vim /etc/fstab
      #
      # /etc/fstab
      # Created by anaconda on Mon Mar 30 10:57:59 2020
      #
      # Accessible filesystems, by reference, are maintained under '/dev/disk'
      # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
      #
      /dev/mapper/rhel-root   /                       xfs     defaults        1 1
      UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot                   xfs     defaults,uquota        1 2
      /dev/mapper/rhel-swap   swap                    swap    defaults        0 0
      /dev/sdb1               /newFS                  xfs     defaults        0 0
      /dev/sdb2               /newFS2                 xfs     defaults        0 0
      /dev/sdb3               swap                    swap    defaults        0 0
      #del
      /dev/herhan/vo /herhan ext4 defaults 0 0
    ```

  * 2.删除逻辑卷设备，需要输入y来确认操作。

    ```shell
      [root@localhost Desktop]# lvremove /dev/herhan/vo
      Do you really want to remove active logical volume vo? [y/n]: y
      Logical volume "vo" successfully removed
    ```

  * 3.删除卷组，此处只写卷组名称即可，不需要设备的绝对路径。

    ```shell
      [root@localhost Desktop]# vgremove herhan
      Volume group "herhan" successfully removed
    ```

  * 4.删除物理卷设备。

    ```shell
      [root@localhost Desktop]# pvremove /dev/sdc /dev/sdd /dev/sde /dev/sdf
      Labels on physical volume "/dev/sdc" successfully wiped
      Labels on physical volume "/dev/sdd" successfully wiped
      Labels on physical volume "/dev/sde" successfully wiped
      Labels on physical volume "/dev/sdf" successfully wiped
    ```

  * 5.在上述操作执行完毕之后，再执行lvdisplay、vgdisplay、pvdisplay命令来查看LVM的信息时就不会再看到信息了（前提是上述步骤的操作是正确的）。
