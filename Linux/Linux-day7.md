# Linux_day7 使用RAID与LVM磁盘阵列技术

---

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

## LVM逻辑卷管理器