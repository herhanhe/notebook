# Linux-day6 存储结构与磁盘划分

---

## 1.一切从“/”开始

* `Linux`系统中一切都是文件。
* `Linux`系统中的一切文件都是从“根（/）”目录开始的，并按照温文件系统层次化标准（`FHS，Filesystem Hierarchy Standard`）采用树形结构来存放文件，以及定义了常见目录的用途。
* `Linux`系统中的文件和目录名称是严格区分大小写的。

* `Linux`系统中常见的目录名称以及相应内容

    目录名称|应放置文件的内容
    -|-
    `/boot`|开机所需文件——内核、开机菜单以及所需配置文件等
    `/dev`|以文件形式存放任何设备与接口
    `/etc`|配置文件
    `/home`|用户家目录
    `/bin`|存放单用户模式下还可以操作的命令
    `/lib`|开机时用到的函数库，以及`/bin`与`/sbin`下面的命令要调用的函数
    `/sbin`|开机过程中需要的命令
    `/media`|用于挂载设备文件的目录
    `/opt`|放置第三方的软件
    `/root`|系统管理员的家目录
    `/src`|一些网络服务的数据文件目录
    `/tmp`|任何人均可使用的“共享”临时目录
    `/proc`|虚拟文件系统，例如系统内核、进程、外部设备及网络状态等
    `/usr/local`|用户自行安装的软件
    `/usr/sbin`|`Linux`系统开机时不会使用到的软件/命令/脚本
    `/usr/share`|帮助与说明文件，也可放置共享文件
    `/var`|主要存放经常变化的文件，如日志
    `/lost+found`|当文件系统发生错误时，将一些丢失的文件片段存放在这里

* 路径
  * 路径指的是如何定位到某个文件，分为绝对路径与相对路径。
    * 绝对路径（`absolute path`）:指的是从根目录（/）开始写起的文件或目录名称
    * 相对路径（`relative path`）:指的是相对于当前路径的写法。

## 2.物理设备的命名规则

* 系统内核中的`udev`设备管理器会自动把硬件名称规范起来，目的是让用户通过设备文件的名字可以猜出舍必大致的属性以及分区信息等；
* `udev`设备管系统的服务会一直以守护进程的形式运行并监听内核发出的信号来管理`/dev`目录下的设备文件。
* 常见的硬件设备及其文件名称
  
  硬件设备|文件名称
  -|-
  `IDE`设备|`/dev/hd[a-d]`
  `SCSI/SATA/U盘`|`/dev/sd[a-p]`
  软驱|`/dev/fd[0-1]`
  打印机|`/dev/lp[0-15]`
  光驱|`/dev/cdrom`
  鼠标|`/dev/mouse`
  磁带机|`/dev/st0 或 /dev/ht0`

* 硬盘设备
  * 一般的硬盘设备都会是以`/dev/sd`开头的。系统采用a~p来代表16块不同的硬盘（默认从a开始分配）
  * 硬盘分区的编号：
    * 主分区或扩展分区的编号从1开会，到4结束。
    * 逻辑分区从编号5开始。

    ![6-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586186848761.png)

  * 扇区：
    * 硬盘设备是由大量的扇区组成的
    * 每个扇区的容量为512字节
    * 第一扇区最重要，它里面保存着主引导记录与分区表信息。
      * 主引导记录需要占用446字节，分区表为64字节，结束符占用2字节；
      * 其中分区表中每记录一个分区信息就需要16字节，
      * 这样一来最多只有4个分区信息可以写到第一扇区中，这4个分区就是4个主分区。

    ![6-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586264016297.png)

    * 解决分区个数不足的问题，可以将第一扇区的分区表中16字节（原本要写入主分区信息）的空间（称之为扩展分区）拿出来指向另外一个分区。
    * 扩展分区，严格地讲它不是一个实际意义的分区，它仅仅是一个指向下一个分区的指针，这种指针结构将形成一个单向链表；
    * 逻辑分区，在扩展分区中创建出的，从而满足分区大于4个

    ![6-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586423610326.png)

## 3.文件系统与数据资料

* 文件系统
  * 用户再硬件存储设备中执行的文件建立、写入、读取、修改、转存、与控制等操作都是依靠文件系统来完成的。
  * 作用是合理规划硬盘，以确保用户正常的使用需求。
  * 常见的文件系统：
    * `Ext3`：
      * 是一款日志文件系统，能够在系统异常宕机时避免文件系统资料丢失，并能自动修复数据的不一致与错误。
      * 当硬盘容量较大时，所需的修复时间也会很长
      * 不能百分之百保证资料不会丢失。
      * 他会把整个磁盘的每个写入动作的细节都预先记录下来，以便在宕机时能回溯追踪到被中断的部分，然后尝试进行修复。
    * `Ext4`：
      * `Ext3`的改进版本，
      * `RHEL6`系统中的默认文件管理系统
      * 支持的存储容量高达1EB
      * 能够有无限多的子目录
      * 文件系统能够批量分配block块，从而极大地提高速写效率
    * `XFS`：
      * 是一种高性能的日志文件系统
      * `RHEL7`系统中的默认文件管理系统
      * 优势：在发生意外宕机后，既可以快速恢复可能被破坏的文件，而且强大的日志功能只用花费极低的计算和存储性能
      * 支持的存储容量高达18EB
* 数据
  * `inode`：
    * `Linux`中记录每个文件的权限与属性
    * 每个文件占用一个独立的`inode`表格，大小默认128字节
    * 记录的信息：
      * 该文件的访问权限（`read、write、execute`）
      * 该文件的所有者与所属组（`owner、group`）
      * 该文件的大小（`size`）
      * 该文件的创建或内容修改时间（`ctime`）
      * 该文件的最后一次访问时间（`atime`）
      * 该文件的修改时间（`mtime`）
      * 文件的特殊权限（`SUID、SGID、SBIT`）
      * 该文件的真实数据地址（1point1）
  * `block`：
    * 文件的实际内容保存处
    * `block`快大小（1KB、2KB、4KB）
    * 当文件的`inode`被写满后，`Linux`系统会自动分配出一个`block`块，专门用于像`inode`那样记录其他`block`块的信息，这样把各个`block`块的内容串到一起，就能够让用户读到完整的文件内容了。

* 虚拟文件系统（`VFS Virual File System`）
  * 为了使用户在读取或写入文件时不用关心底层的硬件结构
  * `Linux`内核中的软件层为用户提供了一个`VFS`接口
  * 用户实际上在操作文件时就是统一对虚拟文件系统操作

  ![6-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586423620299.png)

## 4.挂载硬件设备

* 挂载
  * 当用户需要使用硬盘设备或分区中的数据时，需要先将其与一个已存在的目录文件进行关联，而这个关联动作就是“挂载”
* `mount`命令：
  * 用与挂载文件系统
  * 格式为“`mount` 文件系统 挂载目录”
  * 参数于作用：
  
    参数|作用
    -|-
    `-a`|挂载所有在`/etc/fstab`中定义的文件系统
    `-t`|指定文件系统的类型

  * 范例：
  
    ```shell
      mount /dev/sd2 /backup
    ```

* `/etc/fstab`文件：
  * 让硬件设备和目录永久地进行自动关联
  * 填写格式：“设备文件  格式类型  权限选项  是否备份  是否自检”
  
    字段|意义
    -|-
    设备文件|一般为设备的路径+设备名称，也可以写唯一识别码（`UUID，Universally Unique Identifier`）
    挂载目录|指定要挂载到的目录，需在挂载前创建好
    格式类型|指定文件系统的格式，比如`Ext3、Ext4、XFS、SWAP、iso9660（此为光盘设备）`等
    权限选项|若设置为`defaults`,则默认权限为：`rw、suid、dev、exec、auto、nouser、async`
    是否备份|若为1则开机后使用`dump`进行磁盘备份，为0则不备份
    是否自检|若为1则开机后自动进行磁盘自检，为0则不自检
  * 范例：

    ```shell
      [root@centos ~]# cat /etc/fstab

      #
      # /etc/fstab
      # Created by anaconda on Fri Mar  2 14:59:15 2018
      #
      # Accessible filesystems, by reference, are maintained under '/dev/disk'
      # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
      #
      UUID=74309cb6-4564-422f-bd07-a13e35acbb7a /                       xfs     defaults        0 0
      /dev/sdb2  /backup     ext4     defaults        0 0
      ```

* `umount`命令：
  * 卸载，用于撤销已经挂载的设备文件
  * 格式：“`umount` [挂载点/设备文件]”
  * 范例：

  ```shell
    [root@centos ~]# umount /dev/sdb2
  ```

## 5.添加硬盘设备

* `fdisk`命令：
  * 用于管理磁盘分区，它提供了集添加、删除、转换分区等功能与一身的“一站式分区服务”
  * 格式：“`fdisk` [磁盘名称]”
  * 参数：
    参数|作用
    -|-
    `m`|查看全部可用的参数
    `n`|添加新的分区
    `d`|删除某个分区信息
    `l`|列出所有可能的分区类型
    `t`|改变某个分区的类型
    `p`|查看分区表信息
    `w`|保存并退出
    `q`|不保存直接退出
  * 一般添加硬盘流程:
    * 1.首先使用`fdisk`命令来尝试管理`/dev/sdb`硬盘设备。在看到提示信息后输入参数p来查看硬盘设备内已有的分区信息，其中包括了硬盘的容量大小、扇区个数等信息：

      ```shell
        [root@localhost Desktop]# fdisk /dev/sdb
        Welcome to fdisk (util-linux 2.23.2).

        Changes will remain in memory only, until you decide to write them.
        Be careful before using the write command.


        Command (m for help): p

        Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
        Units = sectors of 1 * 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disk label type: dos
        Disk identifier: 0x1fef086c

        Device Boot      Start         End      Blocks   Id  System
        /dev/sdb1        2048      4196351     2097152   83  Linux
      ```

    * 2.输入参数`n`尝试添加新的分区。系统会要求您是选择继续输入参数`p`来创建主分区，还是输入参数`e`来创建扩展分区：

      ```shell
        Command (m for help): n
        Partition type:
        p   primary (1 primary, 0 extended, 3 free)
        e   extended
        Select (default p):
      ```

    * 3.在确认创建一个主分区后，系统需要您先输入主分区的编号。接下来系统会提示定义起始的扇区位置，这不需要改动，我们敲击回车键保留默认设置即可，系统会自动计算出最靠前的空闲扇区的位置。最后，系统会要求定义分区的结束扇区位置，这其实就是要去定义整个分区的大小是多少。

      ```shell
        Partition number (2-4, default 2): 
        First sector (4196352-41943039, default 4196352): 
        Using default value 4196352
        Last sector, +sectors or +size{K,M,G} (4196352-41943039, default 41943039): +3G
        Partition 2 of type Linux and of size 3 GiB is set
      ```

    * 4.再次使用参数`p`来查看硬盘设备中的分区信息。确认分区信息，然后敲击参数`w`后回车，这样分区信息写入成功。

      ```shell
        Command (m for help): p

        Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
        Units = sectors of 1 * 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disk label type: dos
        Disk identifier: 0x1fef086c

          Device Boot      Start         End      Blocks   Id  System
        /dev/sdb1            2048     4196351     2097152   83  Linux
        /dev/sdb2         4196352    10487807     3145728   83  Linux

        Command (m for help): w
        The partition table has been altered!

        Calling ioctl() to re-read partition table.

        WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
        The kernel still uses the old table. The new table will be used at
        the next reboot or after you run partprobe(8) or kpartx(8)
        Syncing disks.
      ```

    * 5.上述执行完毕之后，`Linux`系统会自动把这个硬盘主分区抽象成`/dev/sdb2`设备文件。使用`file`命令查看该文件的属性来确认设备文件是否建立，如果出现未建立的Bug，可以输入`partprobe`命令手动将分区信息同步到内核。

      ```shell
        [root@localhost Desktop]# file /dev/sdb2
        /dev/sdb2: cannot open (No such file or directory)
        [root@localhost Desktop]# partprobe
        [root@localhost Desktop]# partprobe
        [root@localhost Desktop]# file /dev/sdb2
        /dev/sdb2: block special
      ```

    * 6.格式化硬件存储设备，告知系统怎么在其上写入数据。`mkfs`命令用于格式化操作,用起来非常简单 —— mkfs.文件类型名称。

      ```shell
        [root@localhost Desktop]# mkfs
        mkfs  mkfs.btrfs  mkfs.cramfs  mkfs.ext2   mkfs.ext3   mkfs.ext4    mkfs.fat     mkfs.minix   mkfs.msdos   mkfs.vfat    mkfs.xfs
      ```

      * 例如 格式分区为`XFS`的文件系统

      ```shell
        [root@localhost Desktop]# mkfs.xfs /dev/sdb2
        meta-data=/dev/sdb2          isize=256    agcount=4, agsize=196608 blks
                 =                   sectsz=512   attr=2, projid32bit=1
                 =                   crc=0
        data     =                   bsize=4096   blocks=786432, imaxpct=25
                 =                   sunit=0      swidth=0 blks
        naming   =version 2          bsize=4096   ascii-ci=0 ftype=0
        log      =internal log       bsize=4096   blocks=2560, version=2
                 =                   sectsz=512   sunit=0 blks, lazy-count=1
        realtime =none               extsz=4096   blocks=0, rtextents=0
      ```

    * 7.创建一个用于挂载设备的挂载点目录；使用`mount`命令将存储设备挂载；通过`df -h`命令来查看挂载状态和硬盘使用量信息。最后通过添加挂载信息到`/etc/fstab`永久挂载。

     ```shell
       [root@localhost Desktop]# mkdir /newFS2
       [root@localhost Desktop]# mount /dev/sdb2 /newFS2/
       [root@localhost Desktop]# df -h
       Filesystem             Size  Used Avail Use% Mounted on
       /dev/mapper/rhel-root   18G  2.9G   15G  17% /
       devtmpfs               985M     0  985M   0% /dev
       tmpfs                  994M  140K  994M   1% /dev/shm
       tmpfs                  994M  8.8M  986M   1% /run
       tmpfs                  994M     0  994M   0% /sys/fs/cgroup
       /dev/sda1              497M  119M  379M  24% /boot
       /dev/sdb1              2.0G   33M  2.0G   2% /newFS
       /dev/sdb2              3.0G   33M  3.0G   2% /newFS2
     ```

     ```shell
       [root@localhost Desktop]# vim /etc/fstab
       #
       # /etc/fstab
       # Created by anaconda on Mon Mar 30 10:57:59 2020
       #
       # Accessible filesystems, by reference, are maintained under '/dev/disk'
       # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
       #
       /dev/mapper/rhel-root   /                xfs     defaults        1 1
       UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot                   xfs     defaults        1 2
       /dev/mapper/rhel-swap   swap             swap    defaults        0 0
       /dev/sdb1               /newFS           xfs     defaults        0 0
       /dev/sdb2               /newFS2          xfs     defaults        0 0
     ```

* `du`命令：
  * 就是用来查看一个或多个文件占用了多大的硬盘空间。
  * 格式：“`du` [选项][文件]”
  * 使用`du -sh /*`命令来查看在`Linux`系统根目录下所有一级目录分别占用的空间大小。

    ```shell
      [root@localhost Desktop]# du -sh /newFS/
      33M /newFS/
    ```

## 6.添加交换分区

* `SWAP`(交换)分区
  * 是通过在硬盘中预先划出一定空间，然后将把内存中暂时不常用的数据临时存放到硬盘中，以腾出物理内存空间让更活跃的程序服务来使用。
  * 设计目的是为了解决真实物理内存不足的问题。
  * 只有当真实的物理内存耗尽后才会调用交换分区的资源。
  * 交换分区划分建议：在生产环境中，交换分区的大小一般为真实物理内存的1.5~2倍。
  * 创建步奏：
    * 1.从硬盘中分出需要的空间。

      ```shell
        [root@localhost Desktop]# fdisk /dev/sdb
        Welcome to fdisk (util-linux 2.23.2).

        Changes will remain in memory only, until you decide to write them.
        Be careful before using the write command.


        Command (m for help): p

        Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
        Units = sectors of 1 * 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disk label type: dos
        Disk identifier: 0x1fef086c

        Device Boot      Start         End      Blocks   Id  System
        /dev/sdb1         2048     4196351     2097152   83  Linux
        /dev/sdb2      4196352    10487807     3145728   83  Linux

        Command (m for help): n
        Partition type:
        p   primary (2 primary, 0 extended, 2 free)
        e   extended
        Select (default p): p
        Partition number (3,4, default 3):
        First sector (10487808-41943039, default 10487808):
        Using default value 10487808
        Last sector, +sectors or +size{K,M,G} (10487808-41943039, default 41943039): +5G
        Partition 3 of type Linux and of size 5 GiB is set

        Command (m for help): p

        Disk /dev/sdb: 21.5 GB, 21474836480 bytes, 41943040 sectors
        Units = sectors of 1 * 512 = 512 bytes
        Sector size (logical/physical): 512 bytes / 512 bytes
        I/O size (minimum/optimal): 512 bytes / 512 bytes
        Disk label type: dos
        Disk identifier: 0x1fef086c

        Device Boot      Start         End      Blocks   Id  System
        /dev/sdb1            2048     4196351     2097152   83  Linux
        /dev/sdb2         4196352    10487807     3145728   83  Linux
        /dev/sdb3        10487808    20973567     5242880   83  Linux

        Command (m for help): w
        The partition table has been altered!

        Calling ioctl() to re-read partition table.

        WARNING: Re-reading the partition table failed with error 16: Device or resource busy.
        The kernel still uses the old table. The new table will be used at
        the next reboot or after you run partprobe(8) or kpartx(8)
        Syncing disks.
        [root@localhost Desktop]# file /dev/sdb3
        /dev/sdb3: cannot open (No such file or directory)
        [root@localhost Desktop]# partprobe
        [root@localhost Desktop]# partprobe
        [root@localhost Desktop]# file /dev/sdb3
        /dev/sdb3: block special
      ```

    * 2.使用`SWAP`分区专用的格式化命令`mkswap`,对新建的主分区进行格式化操作：

      ```shell
        [root@localhost Desktop]# mkswap /dev/sdb3
        Setting up swapspace version 1, size = 5242876 KiB
        no label, UUID=6ca4483e-1ca7-44a8-b164-1dcc13864224
      ```

    * 3.使用`swapon`命令把`SWAP`分区设备挂载到系统中。通过`free -m`命令来查看交换分区的大小变化。

      ```shell
        [root@localhost Desktop]# swapon /dev/sdb3
        [root@localhost Desktop]# free -m
                total       used       free     shared    buffers     cached
        Mem:     1987       1285        702          9          1        397
        -/+ buffers/cache:        886       1101
        Swap:    7167          0       7167
      ```

    * 4.最后通过添加挂载信息到`/etc/fstab`永久挂载
  
      ```shell
        [root@localhost Desktop]# vim /etc/fstab
        #
        # /etc/fstab
        # Created by anaconda on Mon Mar 30 10:57:59 2020
        #
        # Accessible filesystems, by reference, are maintained under '/dev/disk'
        # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
        #
        /dev/mapper/rhel-root   /         xfs     defaults        1 1
        UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot                   xfs     defaults        1 2
        /dev/mapper/rhel-swap   swap      swap    defaults        0 0
        /dev/sdb1               /newFS    xfs     defaults        0 0
        /dev/sdb2               /newFS2   xfs     defaults        0 0
        /dev/sdb3               swap      swap    defaults        0 0
      ```

## 7.磁盘容量配额

* root管理员就需要使用磁盘容量配额服务来限制某位用户或某个用户组针对特定文件夹可以使用的最大硬盘空间或最大文件个数，一旦达到这个最大值就不再允许继续使用。
* `quota`命令
  * 进行磁盘容量配额管理
  * 软限制：当达到软限制时会提示用户，但仍允许用户在限定的额度内继续使用。
  * 硬限制：当达到硬限制时会提示用户，且强制终止用户的操作。
* 在配置开始时需手动编辑`/etc/fstab`文件，让该目录能改支持`quota`磁盘容量配额服务；在`RHEL7`之前版本的系统中，需要使用`usrquota`参数，而`RHEL7`系统使用的则是`uquota`参数。
* 实例：
  
  ```shell
    [root@localhost Desktop]# vim /etc/fstab
    #
    # /etc/fstab
    # Created by anaconda on Mon Mar 30 10:57:59 2020
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    /dev/mapper/rhel-root   /                xfs     defaults        1 1
    UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot      xfs     defaults,uquota        1 2
    /dev/mapper/rhel-swap   swap             swap    defaults        0 0
    /dev/sdb1               /newFS           xfs     defaults        0 0
    /dev/sdb2               /newFS2          xfs     defaults        0 0
    /dev/sdb3               swap             swap    defaults        0 0
    [root@localhost Desktop]# reboot
    [root@localhost Desktop]# mount | grep boot
    /dev/sda1 on /boot type xfs (rw,relatime,seclabel,attr2,inode64,usrquota)
  ```

* `xfs_quota`命令：
  * 是一个专门针对`XFS`文件系统来管理`quota`磁盘容量配额服务而设计的命令
  * 格式为“`xfs_quota` [参数] 配额 文件系统”
  * 参数：
    * `-c`：用于以参数形式设置需要执行的命令；
    * `-x`：专家模式，让运维人员能够对`quota`服务进行更多复杂的配置
  * 实例：设置用户tom对`/boot`目录的`quota`磁盘容量配额，具体的限额控制包括：硬盘使用量的软限制和硬限制分别为3MB和6MB；创建文件数量的软限制和硬限制分别为3个和6个。

    ```shell
      [root@localhost Desktop]# useradd tom
      [root@localhost Desktop]# chmod -Rf o+w /boot
      [root@localhost Desktop]# xfs_quota -x -c 'limit bsoft=3m bhard=6m isoft=3 ihard=6 tom' /boot
      [root@localhost Desktop]# xfs_quota -x -c report /boot
      User quota on /boot (/dev/sda1)        Blocks
      User ID          Used       Soft       Hard    Warn/Grace
      ---------- --------------------------------------------------
      root            95388          0          0     00 [--------]
      tom                 0       3072       6144     00 [--------]

      [root@localhost Desktop]# su - tom
      [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom bs=5M count=1
      1+0 records in
      1+0 records out
      5242880 bytes (5.2 MB) copied, 0.00575965 s, 910 MB/s
      [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
      dd: error writing ‘/boot/tom’: Disk quota exceeded
      1+0 records in
      0+0 records out
      6291456 bytes (6.3 MB) copied, 0.0277331 s, 227 MB/s
    ```

* `edquota`命令：
  * 用于编辑用户的`edquota`配额限制。
  * 格式为“edquota [参数] [用户] ”
  * 参数：
    * `-u`：参数表示要针对哪个用户进行设置
    * `-g`：参数表示要针对那个工作组进行设置
  * 实例：把用户tom的硬盘使用量的硬限额从5MB提升到8MB：

    ```shell
      [root@localhost Desktop]# edquota -u tom
      Disk quotas for user tom (uid 1001):
      Filesystem      blocks       soft       hard     inodes     soft     hard
      /dev/sda1         8192       3072       8192          1        3        6
      [root@localhost Desktop]# su - tom
      Last login: Sun Apr 12 13:30:45 CST 2020 on pts/1
      Last failed login: Sun Apr 12 13:52:53 CST 2020 on pts/1
      There was 1 failed login attempt since the last successful login.
      [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom bs=8M count=1
      1+0 records in
      1+0 records out
      8388608 bytes (8.4 MB) copied, 0.0444553 s, 189 MB/s
      [tom@localhost ~]$ dd if=/dev/zero of=/boot/tom bs=10M count=1
      dd: error writing ‘/boot/tom’: Disk quota exceeded
      1+0 records in
      0+0 records out
      8388608 bytes (8.4 MB) copied, 0.0165621 s, 506 MB/s
    ```

## 8.软硬方式链接

* 硬链接（`hard link`）:
  * 实际上是指向原文件inode的指针，因此即便原始文件被删除，依然可以通过硬链接文件来访问
  * 需要注意的是，由于技术的局限性，我们不能跨分区对目录文件进行链接。
  * 每添加一个硬链接，该文件的inode连接数就会增加1；而且只有当该文件的inode连接数为0时，才算彻底将它删除。
* 软链接（也称为符号链接[`symbolic link`]）:
  * 仅仅包含所链接文件的路径名，因此能链接目录文件，也可以跨越文件系统进行链接
  * 当原始文件被删除后，链接文件也将失效

* `ln`命令：
  * 用于创建链接文件
  * 格式为“`ln` [选项] 目标”
  * 参数：
    参数|作用
    -|-
    `-s`|创建“符号链接” （如果不带`-s`参数，则默认创建硬链接）
    `-f`|强制创建文件或目录的链接
    `-i`|覆盖前先询问
    `-v`|显示创建链接的过程

  * 实例：软链接

    ```shell
      [tom@localhost ~]$ echo "Welcome to China" > readme.txt
      [tom@localhost ~]$ ln -s readme.txt readit.txt
      [tom@localhost ~]$ cat readme.txt
      Welcome to China
      [tom@localhost ~]$ cat readit.txt
      Welcome to China
      [tom@localhost ~]$ ls -l readme.txt
      -rw-rw-r--. 1 tom tom 17 Apr 12 14:39 readme.txt
      [tom@localhost ~]$ rm -f readme.txt
      [tom@localhost ~]$ cat readit.txt
      cat: readit.txt: No such file or directory
    ```

  * 实例：硬链接

    ```shell
      [tom@localhost ~]$ echo "Welcome to China" > readme.txt
      [tom@localhost ~]$ ln readme.txt readit.txt
      [tom@localhost ~]$ cat readit.txt
      Welcome to China
      [tom@localhost ~]$ cat readme.txt
      Welcome to China
      [tom@localhost ~]$ ls -l readme.txt
      -rw-rw-r--. 2 tom tom 17 Apr 12 14:45 readme.txt
      [tom@localhost ~]$ rm -f readme.txt
      [tom@localhost ~]$ cat readit.txt
      Welcome to China
    ```
