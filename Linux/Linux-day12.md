# Linux-day12 使用`Samba`或`NFS`实现文件共享

---

## 12.1 `Samba`文件共享服务

### 12.1.1 `Samba`服务的历史
  
* 1987年，微软公司和英特尔公司共同制定了`SMB`（`Server Messages Block`，服务器消息块）协议，旨在解决局域网内的文件或打印机等资源的共享问题，这也使得在多个主机之间共享文件变得越来越简单。
* 1991年，当时还在读大学的`Tridgwell`为了解决`Linux`系统与`Windows`系统之间的文件共享问题，基于`SMB`协议开发出了`SMBServer`服务程序。这是一款开源的文件共享软件，经过简单配置就能够实现`Linux`系统与`Windows`系统之间的文件共享工作。当时，`Tridgwell`想把这款软件的名字`SMBServer`注册成为商标，但却被商标局以`SMB`是没有意义的字符而拒绝了申请。后来`Tridgwell`不断翻看词典，突然看到一个拉丁舞蹈的名字—`Samba`，而且这个热情洋溢的舞蹈名字中又恰好包含了“`SMB`”，于是`Samba`服务程序的名字由此诞生。
* `Samba`服务程序现在已经成为在`Linux`系统与`Windows`系统之间共享文件的最佳选择。

### 12.1.2 `Samba`服务的安装

* `Samba`服务程序的配置方法与之前讲解的很多服务的配置方法类似，首先需要先通过`Yum`软件仓库来安装`Samba`服务程序（`Samba`服务程序的名字也恰巧是软件包的名字）：

  ```shell
    [root@localhost ~]# yum install samba
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    rhel7                                                                                                                                                               | 4.1 kB  00:00:00
    Resolving Dependencies
    --> Running transaction check
    ---> Package samba.x86_64 0:4.1.1-31.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ===========================================================================================================================================================================================
    Package                                    Arch                                        Version                                           Repository                                  Size
    ===========================================================================================================================================================================================
    Installing:
    samba                                      x86_64                                      4.1.1-31.el7                                      rhel7                                      527 k

    Transaction Summary
    ===========================================================================================================================================================================================
    Install  1 Package

    Total download size: 527 k
    Installed size: 1.5 M
    Is this ok [y/d/N]: y
    Downloading packages:
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : samba-4.1.1-31.el7.x86_64                                                                                                                                               1/1
    Verifying  : samba-4.1.1-31.el7.x86_64                                                                                                                                               1/1

    Installed:
    samba.x86_64 0:4.1.1-31.el7

    Complete!
  ```

* 安装完毕后打开·Samba服务程序的主配置文件，发现竟然有320行之多！有没有被吓到？但仔细一看就会发现，其实大多数都是以井号（#）开头的注释信息行

  ```shell
    # This is the main Samba configuration file. For detailed information about the
    # options listed here, refer to the smb.conf(5) manual page. Samba has a huge
    # number of configurable options, most of which are not shown in this example.
    #
    # The Official Samba 3.2.x HOWTO and Reference Guide contains step-by-step
    # guides for installing, configuring, and using Samba:
    # http://www.samba.org/samba/docs/Samba-HOWTO-Collection.pdf
    #
    # The Samba-3 by Example guide has working examples for smb.conf. This guide is
    # generated daily: http://www.samba.org/samba/docs/Samba-Guide.pdf
    #
    # In this file, lines starting with a semicolon (;) or a hash (#) are
    # comments and are ignored. This file uses hashes to denote commentary and
    # semicolons for parts of the file you may wish to configure.
    #
    # Note: Run the "testparm" command after modifying this file to check for basic
    # syntax errors.
    #
    #---------------
    # Security-Enhanced Linux (SELinux) Notes:
    #
    # Turn the samba_domain_controller Boolean on to allow Samba to use the useradd
    # and groupadd family of binaries. Run the following command as the root user to
    # turn this Boolean on:
    # setsebool -P samba_domain_controller on
    #
    # Turn the samba_enable_home_dirs Boolean on if you want to share home
    # directories via Samba. Run the following command as the root user to turn this
    # Boolean on:
    # setsebool -P samba_enable_home_dirs on
    #
    # If you create a new directory, such as a new top-level directory, label it
    # with samba_share_t so that SELinux allows Samba to read and write to it. Do
    # not label system directories, such as /etc/ and /home/, with samba_share_t, as
    # such directories should already have an SELinux label.
    #
    # Run the "ls -ldZ /path/to/directory" command to view the current SELinux
    # label for a given directory.
    #
    # Set SELinux labels only on files and directories you have created. Use the
    # chcon command to temporarily change a label:
    # chcon -t samba_share_t /path/to/directory
    #
    # Changes made via chcon are lost when the file system is relabeled or commands
    # such as restorecon are run.
    #
    # Use the samba_export_all_ro or samba_export_all_rw Boolean to share system
    # directories. To share such directories and only allow read-only permissions:
    # setsebool -P samba_export_all_ro on
    # To share such directories and allow read and write permissions:
    # setsebool -P samba_export_all_rw on
    #
    # To run scripts (preexec/root prexec/print command/...), copy them to the
    # /var/lib/samba/scripts/ directory so that SELinux will allow smbd to run them.
    # Note that if you move the scripts to /var/lib/samba/scripts/, they retain
    # their existing SELinux labels, which may be labels that SELinux does not allow
    # smbd to run. Copying the scripts will result in the correct SELinux labels.
    # Run the "restorecon -R -v /var/lib/samba/scripts" command as the root user to
    # apply the correct SELinux labels to these files.
    #
    #--------------
    #
    #======================= Global Settings =====================================

    [global]

    # ----------------------- Network-Related Options -------------------------
    #
    # workgroup = the Windows NT domain name or workgroup name, for example, MYGROUP.
    #
    # server string = the equivalent of the Windows NT Description field.
    #
    # netbios name = used to specify a server name that is not tied to the hostname.
    #
    # interfaces = used to configure Samba to listen on multiple network interfaces.
    # If you have multiple interfaces, you can use the "interfaces =" option to
    # configure which of those interfaces Samba listens on. Never omit the localhost
    # interface (lo).
    #
    # hosts allow = the hosts allowed to connect. This option can also be used on a
    # per-share basis.
    #
    # hosts deny = the hosts not allowed to connect. This option can also be used on
    # a per-share basis.
    #
    # max protocol = used to define the supported protocol. The default is NT1. You
    # can set it to SMB2 if you want experimental SMB2 support.
    #
            workgroup = MYGROUP
            server string = Samba Server Version %v

    ;       netbios name = MYSERVER

    ;       interfaces = lo eth0 192.168.12.2/24 192.168.13.2/24
    ;       hosts allow = 127. 192.168.12. 192.168.13.

    ;       max protocol = SMB2

    # --------------------------- Logging Options -----------------------------
    #
    # log file = specify where log files are written to and how they are split.
    #
    # max log size = specify the maximum size log files are allowed to reach. Log
    # files are rotated when they reach the size specified with "max log size".
    #

            # log files split per-machine:
            log file = /var/log/samba/log.%m
            # maximum size of 50KB per log file, then rotate:
            max log size = 50

    # ----------------------- Standalone Server Options ------------------------
    #
    # security = the mode Samba runs in. This can be set to user, share
    # (deprecated), or server (deprecated).
    #
    # passdb backend = the backend used to store user information in. New
    # installations should use either tdbsam or ldapsam. No additional configuration
    # is required for tdbsam. The "smbpasswd" utility is available for backwards
    # compatibility.
    #

            security = user
            passdb backend = tdbsam


    # ----------------------- Domain Members Options ------------------------
    #
    # security = must be set to domain or ads.
    #
    # passdb backend = the backend used to store user information in. New
    # installations should use either tdbsam or ldapsam. No additional configuration
    # is required for tdbsam. The "smbpasswd" utility is available for backwards
    # compatibility.
    #
    # realm = only use the realm option when the "security = ads" option is set.
    # The realm option specifies the Active Directory realm the host is a part of.
    #
    # password server = only use this option when the "security = server"
    # option is set, or if you cannot use DNS to locate a Domain Controller. The
    # argument list can include My_PDC_Name, [My_BDC_Name], and [My_Next_BDC_Name]:
    #
    # password server = My_PDC_Name [My_BDC_Name] [My_Next_BDC_Name]
    #
    # Use "password server = *" to automatically locate Domain Controllers.

    ;       security = domain
    ;       passdb backend = tdbsam
    ;       realm = MY_REALM

    ;       password server = <NT-Server-Name>

    # ----------------------- Domain Controller Options ------------------------
    #
    # security = must be set to user for domain controllers.
    #
    # passdb backend = the backend used to store user information in. New
    # installations should use either tdbsam or ldapsam. No additional configuration
    # is required for tdbsam. The "smbpasswd" utility is available for backwards
    # compatibility.
    #
    # domain master = specifies Samba to be the Domain Master Browser, allowing
    # Samba to collate browse lists between subnets. Do not use the "domain master"
    # option if you already have a Windows NT domain controller performing this task.
    #
    # domain logons = allows Samba to provide a network logon service for Windows
    # workstations.
    #
    # logon script = specifies a script to run at login time on the client. These
    # scripts must be provided in a share named NETLOGON.
    #
    # logon path = specifies (with a UNC path) where user profiles are stored.
    #
    #
    ;       security = user
    ;       passdb backend = tdbsam

    ;       domain master = yes
    ;       domain logons = yes

            # the following login script name is determined by the machine name
            # (%m):
    ;       logon script = %m.bat
            # the following login script name is determined by the UNIX user used:
    ;       logon script = %u.bat
    ;       logon path = \\%L\Profiles\%u
            # use an empty path to disable profile support:
    ;       logon path =

            # various scripts can be used on a domain controller or a stand-alone
            # machine to add or delete corresponding UNIX accounts:

    ;       add user script = /usr/sbin/useradd "%u" -n -g users
    ;       add group script = /usr/sbin/groupadd "%g"
    ;       add machine script = /usr/sbin/useradd -n -c "Workstation (%u)" -M -d /nohome -s /bin/false "%u"
    ;       delete user script = /usr/sbin/userdel "%u"
    ;       delete user from group script = /usr/sbin/userdel "%u" "%g"
    ;       delete group script = /usr/sbin/groupdel "%g"


    # ----------------------- Browser Control Options ----------------------------
    #
    # local master = when set to no, Samba does not become the master browser on
    # your network. When set to yes, normal election rules apply.
    #
    # os level = determines the precedence the server has in master browser
    # elections. The default value should be reasonable.
    #
    # preferred master = when set to yes, Samba forces a local browser election at
    # start up (and gives itself a slightly higher chance of winning the election).
    #
    ;       local master = no
    ;       os level = 33
    ;       preferred master = yes

    #----------------------------- Name Resolution -------------------------------
    #
    # This section details the support for the Windows Internet Name Service (WINS).
    #
    # Note: Samba can be either a WINS server or a WINS client, but not both.
    #
    # wins support = when set to yes, the NMBD component of Samba enables its WINS
    # server.
    #
    # wins server = tells the NMBD component of Samba to be a WINS client.
    #
    # wins proxy = when set to yes, Samba answers name resolution queries on behalf
    # of a non WINS capable client. For this to work, there must be at least one
    # WINS server on the network. The default is no.
    #
    # dns proxy = when set to yes, Samba attempts to resolve NetBIOS names via DNS
    # nslookups.

    ;       wins support = yes
    ;       wins server = w.x.y.z
    ;       wins proxy = yes

    ;       dns proxy = yes

    # --------------------------- Printing Options -----------------------------
    #
    # The options in this section allow you to configure a non-default printing
    # system.
    #
    # load printers = when set you yes, the list of printers is automatically
    # loaded, rather than setting them up individually.
    #
    # cups options = allows you to pass options to the CUPS library. Setting this
    # option to raw, for example, allows you to use drivers on your Windows clients.
    #
    # printcap name = used to specify an alternative printcap file.
    #

            load printers = yes
            cups options = raw

    ;       printcap name = /etc/printcap
            # obtain a list of printers automatically on UNIX System V systems:
    ;       printcap name = lpstat
    ;       printing = cups

    # --------------------------- File System Options ---------------------------
    #
    # The options in this section can be un-commented if the file system supports
    # extended attributes, and those attributes are enabled (usually via the
    # "user_xattr" mount option). These options allow the administrator to specify
    # that DOS attributes are stored in extended attributes and also make sure that
    # Samba does not change the permission bits.
    #
    # Note: These options can be used on a per-share basis. Setting them globally
    # (in the [global] section) makes them the default for all shares.

    ;       map archive = no
    ;       map hidden = no
    ;       map read only = no
    ;       map system = no
    ;       store dos attributes = yes


    #============================ Share Definitions ==============================

    [homes]
            comment = Home Directories
            browseable = no
            writable = yes
    ;       valid users = %S
    ;       valid users = MYDOMAIN\%S

    [printers]
            comment = All Printers
            path = /var/spool/samba
            browseable = no
            guest ok = no
            writable = no
            printable = yes

    # Un-comment the following and create the netlogon directory for Domain Logons:
    ;       [netlogon]
    ;       comment = Network Logon Service
    ;       path = /var/lib/samba/netlogon
    ;       guest ok = yes
    ;       writable = no
    ;       share modes = no

    # Un-comment the following to provide a specific roving profile share.
    # The default is to use the user's home directory:
    ;       [Profiles]
    ;       path = /var/lib/samba/profiles
    ;       browseable = no
    ;       guest ok = yes

    # A publicly accessible directory that is read only, except for users in the
    # "staff" group (which have write permissions):
    ;       [public]
    ;       comment = Public Stuff
    ;       path = /home/samba
    ;       public = yes
    ;       writable = yes
    ;       printable = no
    ;       write list = +staff
  ```

* 由于在`Samba`服务程序的主配置文件中，注释信息行实在太多，不便于分析里面的重要参数，因此先把主配置文件改个名字，然后使用`cat`命令读入主配置文件，再在`grep`命令后面添加`-v`参数（反向选择），分别去掉所有以井号（`#`）和分号（`;`）开头的注释信息行，对于剩余的空白行可以使用`^$`参数来表示并进行反选过滤，最后把过滤后的可用参数信息通过重定向符覆盖写入到原始文件名称中。

  ```shell
    [root@localhost ~]# mv /etc/samba/smb.conf /etc/samba/smb.conf.bak
    [root@localhost ~]# cat /etc/samba/smb.conf.bak | grep -v "#" | grep -v ";" | grep -v "^$" > /etc/samba/smb.conf
    [root@localhost ~]# cat /etc/samba/smb.conf
  ```

* Samba服务程序中的参数以及作用

  ```shell
    [global]                                        # 全局参数
        workgroup = MYGROUP                         # 工作组名称
        server string = Samba Server Version %v     # 服务器介绍信息，参数%v为显示SMB版本号
        log file = /var/log/samba/log.%m            # 定义日志文件的存放位置与名称，参数%m为来访的主机号
        max log size = 50                           # 定义日志文件的最大容量为50KB
        security = user                             # 安全验证的方式，总共有4种
        # share：来访主机无需验证口令；比较方便，但安全性很差
        # user：需验证来访主机提供的口令后才可以访问；提升了安全性
        # server：使用独立的远程主机验证来访主机提供的口令（集中管理账户）
        # domain：使用域控制器进行省份验证
        passdb backend = tdbsam                     # 定义用户后台的类型，共有3种
        # smbpasswd：使用smbpasswd命令为系统用户设置Samba服务程序的密码
        # tdbsam：创建数据库文件并使用pdbedit命令建立Samba服务程序的用户
        # ldapsam：基于LADP服务进行账户验证
        load printers = yes                         # 设置在Samba服务启动时是否共享打印机设备
        cups options = raw                          # 打印机的选项
    [homes]                                         # 共享参数
        comment = Home Directories                  # 描述信息
        browseable = no                             # 指定共享信息是否在“网上邻居”中可见
        writable = yes                              # 定义是否可以执行写入操作，与“read only”相反
    [printers]                                      # 打印机共享参数
        comment = All Printers
        path = /var/spool/samba                     # 共享文件的实际路径（重要）
        browseable = no
        guest ok = no                               # 是否所有人可见，等同于“public”参数。
        writable = no
        printable = yes
  ```

### 12.1.3 配置共享资源

* `Samba`服务程序的主配置文件与前面学习过的`Apache`服务很相似，包括全局配置参数和区域配置参数。全局配置参数用于设置整体的资源共享环境，对里面的每一个独立的共享资源都有效。区域配置参数则用于设置单独的共享资源，且仅对该资源有效。创建共享资源的方法很简单，只要将表中的参数写入到`Samba`服务程序的主配置文件中，然后重启该服务即可。

* 用于设置`Samba`服务程序的参数以及作用

  参数|作用
  -|-
  [`database`]|共享名称为`database`
  `comment = Do not arbitrarily modify the database file`|警告用户不要随意修改数据库
  `path = /home/database`|共享目录为`/home/database`
  `pulic = no`|关闭“所有人可见”
  `writable = yes`|允许写入操作

* **第1步**：创建用于访问共享资源的账户信息.在RHEL 7系统中，`Samba`服务程序默认使用的是用户口令认证模式（`user`）。这种认证模式可以确保仅让有密码且受信任的用户访问共享资源，而且验证过程也十分简单。不过，只有建立账户信息数据库之后，才能使用用户口令认证模式。另外，`Samba`服务程序的数据库要求账户必须在当前系统中已经存在，否则日后创建文件时将导致文件的权限属性混乱不堪，由此引发错误。

* `pdbedit`命令用于管理`SMB`服务程序的账户信息数据库，格式为“`pdbedit` [选项] 账户”。在第一次把账户信息写入到数据库时需要使用`-a`参数，以后在执行修改密码、删除账户等操作时就不再需要该参数了。

* 用于`pdbedit`命令的参数以及作用

  参数|作用
  -|-
  `-a`用户名|建立`Samba`用户
  `-x`用户名|删除`Samba`用户
  `-L`|列出用户列表
  `-Lv`|列出用户详细信息的列表

  ```shell
    [root@localhost ~]# id herhan
    uid=1000(herhan) gid=1000(herhan) groups=1000(herhan),10(wheel)
    [root@localhost ~]# pdbedit -a -u herhan
    new password:
    retype new password:
    Unix username:        herhan
    NT username:
    Account Flags:        [U          ]
    User SID:             S-1-5-21-3418601577-2831931643-858006119-1000
    Primary Group SID:    S-1-5-21-3418601577-2831931643-858006119-513
    Full Name:            herhan
    Home Directory:       \\localhost\herhan
    HomeDir Drive:
    Logon Script:
    Profile Path:         \\localhost\herhan\profile
    Domain:               LOCALHOST
    Account desc:
    Workstations:
    Munged dial:
    Logon time:           0
    Logoff time:          Wed, 06 Feb 2036 23:06:39 CST
    Kickoff time:         Wed, 06 Feb 2036 23:06:39 CST
    Password last set:    Sat, 27 Jun 2020 19:17:44 CST
    Password can change:  Sat, 27 Jun 2020 19:17:44 CST
    Password must change: never
    Last bad password   : 0
    Bad password count  : 0
    Logon hours         : FFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFFF
  ```

* **第2步**：创建用于共享资源的文件目录。在创建时，不仅要考虑到文件读写权限的问题，而且由于`/home`目录是系统中普通用户的家目录，因此还需要考虑应用于该目录的`SELinux`安全上下文所带来的限制。在前面对`Samba`服务程序配置文件中的注释信息进行过滤时，这些过滤的信息中就有关于`SELinux`安全上下文策略的说明，我们只需按照过滤信息中有关`SELinux`安全上下文策略中的说明中给的值进行修改即可。修改完毕后执行`restorecon`命令，让应用于目录的新`SELinux`安全上下文立即生效。

  ```shell
    [root@localhost ~]# mkdir /home/database
    [root@localhost ~]# chown -Rf herhan:herhan /home/database
    [root@localhost ~]# semanage fcontext -a -t samba_share_t /home/database
    [root@localhost ~]# restorecon -Rv /home/database
    restorecon reset /home/database context unconfined_u:object_r:home_root_t:s0->unconfined_u:object_r:samba_share_t:s0
  ```

* **第3步**：设置`SELinux`服务与策略，使其允许通过`Samba`服务程序访问普通用户家目录。执行`getsebool`命令，筛选出所有与`Samba`服务程序相关的`SELinux`域策略，根据策略的名称（和经验）选择出正确的策略条目进行开启即可：

  ```shell
    [root@localhost ~]# getsebool -a | grep samba
    samba_create_home_dirs --> off
    samba_domain_controller --> off
    samba_enable_home_dirs --> off
    samba_export_all_ro --> off
    samba_export_all_rw --> off
    samba_portmapper --> off
    samba_run_unconfined --> off
    samba_share_fusefs --> off
    samba_share_nfs --> off
    sanlock_use_samba --> off
    use_samba_home_dirs --> off
    virt_sandbox_use_samba --> off
    virt_use_samba --> off
    [root@localhost ~]# setsebool -P samba_enable_home_dirs on
  ```

* **第4步**：在`Samba`服务程序的主配置文件中，根据表提到的格式写入共享信息。在原始的配置文件中，[`homes`]参数为来访用户的家目录共享信息，[`printers`]参数为共享的打印机设备。这两项如果在今后的工作中不需要，可以手动删除，这没有任何问题。

  ```shell
    [root@localhost ~]# setsebool -P samba_enable_home_dirs on
    [root@localhost ~]# vim /etc/samba/smb.conf
    [global]
            workgroup = MYGROUP
            server string = Samba Server Version %v
            log file = /var/log/samba/log.%m
            max log size = 50
            security = user
            passdb backend = tdbsam
            load printers = yes
            cups options = raw
    [database]
            comment = Do not arbitrarily modify the database file
            path = /home/database
            public = no
            writable = yes
  ```

* **第5步**：`Samba`服务程序的配置工作基本完毕。接下来重启`smb`服务（`Samba`服务程序在`Linux`系统中的名字为`smb`）并清空`iptables`防火墙，然后就可以检验配置效果了。

  ```shell
    [root@localhost ~]# systemctl restart smb
    [root@localhost ~]# systemctl enable smb
    ln -s '/usr/lib/systemd/system/smb.service' '/etc/systemd/system/multi-user.target.wants/smb.service'
    [root@localhost ~]# iptables -F
    [root@localhost ~]# service iptables save
    iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]
  ```

### 12.1.4 `Windows`挂载共享

* 要在`Windows`系统中访问共享资源，只需在`Windows`的“运行”命令框中输入两个反斜杠，然后再加服务器的`IP`地址即可。在`Windows`系统中访问共享资源

  ![12-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/12-1.png)

* 在`RHEL 7`系统中，`Samba`服务程序使用的果然是独立的账户信息数据库。所以，即便在`Linux`系统中有一个`herhan`账户，`Samba`服务程序使用的账户信息数据库中也有一个同名的`herhan`账户，大家也一定要弄清楚它们各自所对应的密码。
* 正确输入`herhan`账户名以及使用`pdbedit`命令设置的密码后，就可以登录到共享界面中了，此时，我们可以尝试执行查看、写入、更名、删除文件等操作。

  ![12-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/12-2.png)

* 由于`Windows`系统的缓存原因，有可能您在第二次登录时提供了正确的账户和密码，依然会报错，这时只需要重新启动一下`Windows`客户端就没问题了（如果`Windows`系统依然报错，请检查上述步骤是否有做错的地方）。

### 12.1.4 `Linux`挂载共享

* `Samba`服务程序还可以实现`Linux`系统之间的文件共享。设置`Samba`服务程序所在主机（即`Samba`共享服务器）和`Linux`客户端使用的`IP`地址，然后在客户端安装支持文件共享服务的软件包（`cifs-utils`）。

  ```shell
    [root@localhost Desktop]# yum install cifs-utils
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    rhel7                                                                                                                                                               | 4.1 kB  00:00:00
    Resolving Dependencies
    --> Running transaction check
    ---> Package cifs-utils.x86_64 0:6.2-6.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ===========================================================================================================================================================================================
    Package                                        Arch                                       Version                                         Repository                                 Size
    ===========================================================================================================================================================================================
    Installing:
    cifs-utils                                     x86_64                                     6.2-6.el7                                       rhel7                                      83 k

    Transaction Summary
    ===========================================================================================================================================================================================
    Install  1 Package

    Total download size: 83 k
    Installed size: 174 k
    Is this ok [y/d/N]: y
    Downloading packages:
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : cifs-utils-6.2-6.el7.x86_64                                                                                                                                             1/1
    Verifying  : cifs-utils-6.2-6.el7.x86_64                                                                                                                                             1/1

    Installed:
    cifs-utils.x86_64 0:6.2-6.el7

    Complete!
  ```

* 在`Linux`客户端，按照`Samba`服务的用户名、密码、共享域的顺序将相关信息写入到一个认证文件中。为了保证不被其他人随意看到，最后把这个认证文件的权限修改为仅`root`管理员才能够读写：

  ```shell
    [root@localhost ~]# vim auth.smb
    [root@localhost ~]# chmod -Rf 600 auth.smb
    username=herhan
    password=123
    domain=MYGROUP
    [root@localhost ~]# chmod -Rf 600 auth.smb
  ```

* 现在，在`Linux`客户端上创建一个用于挂载`Samba`服务共享资源的目录，并把挂载信息写入到`/etc/fstab`文件中，以确保共享挂载信息在服务器重启后依然生效：

  ```shell
    [root@localhost ~]# mkdir /database
    [root@localhost ~]# vim /etc/fstab
    #
    # /etc/fstab
    # Created by anaconda on Mon Mar 30 10:57:59 2020
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    /dev/mapper/rhel-root   /                       xfs     defaults        1 1
    UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot                   xfs     defaults        1 2
    /dev/mapper/rhel-swap   swap                    swap    defaults        0 0
    /dev/cdrom /media/cdrom iso9660 defaults 0 0
    //192.168.10.10/database /database              cifs    credentials=/root/auth.smb      0 0
    [root@localhost ~]# mount -a
  ```

* `Linux`客户端成功地挂载了`Samba`服务的共享资源。进入到挂载目录`/database`后就可以看到`Windows`系统访问`Samba`服务程序时留下来的文件了（即文件`Memo.txt`）。当然，我们也可以对该文件进行读写操作并保存。

  ```shell
    [root@localhost ~]# cat /database/Memo.txt
    I can edit it.
  ```

## 12.2 NFS网络文件系统

* `NFS`（网络文件系统）服务可以将远程`Linux`系统上的文件共享资源挂载到本地主机的目录上，从而使得本地主机（`Linux`客户端）基于`TCP/IP`协议，像使用本地主机上的资源那样读写远程`Linux`系统上的共享文件。

* 由于`RHEL 7`系统中默认已经安装了`NFS`服务，外加`NFS`服务的配置步骤也很简单

* 首先请使用`Yum`软件仓库检查自己的`RHEL 7`系统中是否已经安装了`NFS`软件包：

  ```shell
    [root@localhost ~]# yum install nfs-utils
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    Package 1:nfs-utils-1.3.0-0.el7.x86_64 already installed and latest version
    Nothing to do
  ```

* **第1步**：为了检验`NFS`服务配置的效果，我们需要使用两台`Linux`主机（一台充当`NFS`服务器，一台充当`NFS`客户端）
* 另外，不要忘记清空`NFS`服务器上面`iptables`防火墙的默认策略，以免默认的防火墙策略禁止正常的`NFS`共享服务。

  ```shell
    [root@localhost ~]# iptables -F
    [root@localhost ~]# service iptables save
    iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]
  ```

* **第2步**；在`NFS`服务器上建立用于`NFS`文件共享的目录，并设置足够的权限确保其他人也有写入权限。

  ```shell
    [root@localhost ~]# mkdir /nfsfile
    [root@localhost ~]# chmod -Rf 777 /nfsfile
    [root@localhost ~]# echo "welcome to herhan.com" > /nfsfile/readme
  ```

* **第3步**：`NFS`服务程序的配置文件为`/etc/exports`，默认情况下里面没有任何内容。我们可以按照“共享目录的路径 允许访问的NFS客户端（共享权限参数）”的格式，定义要共享的目录与相应的权限。

* 用于配置`NFS`服务程序配置文件的参数

  参数|作用
  -|-
  `ro`|只读
  `rw`|读写
  `root_squash`|当`NFS`客户端以`root`管理员访问时，映射为`NFS`服务器的匿名用户
  `no_root_squash`|当`NFS`客户端以`root`管理员访问时，映射为`NFS`服务器的root管理员
  `all_squash`|无论`NFS`客户端适用什么账户访问，均映射为`NFS`服务器的匿名用户
  `sync`|同时将数据写入到内存与硬盘中，保存不丢失数据
  `async`|优先将数据保存到内存，然后再写入硬盘；这样效率更高，但可能会丢失数据

* 例如，如果想要把`/nfsfile`目录共享给192.168.10.0/24网段内的所有主机，让这些主机都拥有读写权限，在将数据写入到`NFS`服务器的硬盘中后才会结束操作，最大限度保证数据不丢失，以及把来访客户端`root`管理员映射为本地的匿名用户等，则可以按照下面命令中的格式，将参数写到`NFS`服务程序的配置文件中。

  ```shell
    [root@localhost ~]# vim /etc/exports
    /nfsfile  192.168.10.*(rw,sync,root_squash)
  ```

* **第4步**：启动和启用`NFS`服务程序。由于在使用`NFS`服务进行文件共享之前，需要使用`RPC`（`Remote Procedure Call`，远程过程调用）服务将`NFS`服务器的`IP`地址和端口号等信息发送给客户端。因此，在启动`NFS`服务之前，还需要顺带重启并启用`rpcbind`服务程序，并将这两个服务一并加入开机启动项中。

  ```shell
    [root@localhost ~]# systemctl restart rpcbind
    [root@localhost ~]# systemctl enable rpcbind
    [root@localhost ~]# systemctl restart nfs-server
    [root@localhost ~]# systemctl enable nfs-server
    ln -s '/usr/lib/systemd/system/nfs-server.service' '/etc/systemd/system/nfs.target.wants/nfs-server.service'
  ```

* `NFS`客户端的配置步骤也十分简单。先使用`showmount`命令查询`NFS`服务器的远程共享信息，其输出格式为“共享的目录名称 允许使用客户端地址”。
* `showmount`命令中可用的参数以及作用
  参数|作用
  -|-
  `-e`|显示`NFS`服务器的共享列表
  `-a`|显示本机挂载的文件资源的情况`NFS`资源的情况
  `-v`|显示版本号

  ```shell
    [root@localhost ~]# showmount -e 192.168.10.10
    Export list for 192.168.10.10:
    /nfsfile 192.168.10.*
  ```

* 然后在`NFS`客户端创建一个挂载目录。使用`mount`命令并结合`-t`参数，指定要挂载的文件系统的类型，并在命令后面写上服务器的`IP`地址、服务器上的共享目录以及要挂载到本地系统（即客户端）的目录。

  ```shell
    [root@localhost ~]# mkdir /nfsfile
    [root@localhost ~]# mount -t nfs 192.168.10.10:/nfsfile /nfsfile
  ```

* 挂载成功后就应该能够顺利地看到在执行前面的操作时写入的文件内容了。如果希望`NFS`文件共享服务能一直有效，则需要将其写入到`fstab`文件中：

  ```shell
    [root@localhost ~]# cat /nfsfile/readme
    welcome to herhan.com
    [root@localhost ~]# vim /etc/fstab
    #
    # /etc/fstab
    # Created by anaconda on Mon Mar 30 10:57:59 2020
    #
    # Accessible filesystems, by reference, are maintained under '/dev/disk'
    # See man pages fstab(5), findfs(8), mount(8) and/or blkid(8) for more info
    #
    /dev/mapper/rhel-root   /                       xfs     defaults        1 1
    UUID=6e4ae779-2177-4917-b994-1aa87e9c8180 /boot                   xfs     defaults        1 2
    /dev/mapper/rhel-swap   swap                    swap    defaults        0 0
    /dev/cdrom /media/cdrom iso9660 defaults 0 0
    //192.168.10.10/database /database              cifs    credentials=/root/auth.smb      0 0
    192.168.10.10:/nfsfile   /nfsfile               nfs     defaults        0 0
  ```

## 12.3 `AutoFs`自动挂载服务

* 无论是`Samba`服务还是`NFS`服务，都要把挂载信息写入到`/etc/fstab`中，这样远程共享资源就会自动随服务器开机而进行挂载。虽然这很方便，但是如果挂载的远程资源太多，则会给网络带宽和服务器的硬件资源带来很大负载。如果在资源挂载后长期不使用，也会造成服务器硬件资源的浪费。

* `autofs`自动挂载服务可以帮我们解决这一问题。与`mount`命令不同，`autofs`服务程序是一种`Linux`系统守护进程，当检测到用户试图访问一个尚未挂载的文件系统时，将自动挂载该文件系统。换句话说，我们将挂载信息填入`/etc/fstab`文件后，系统在每次开机时都自动将其挂载，而`autofs`服务程序则是在用户需要使用该文件系统时才去动态挂载，从而节约了网络资源和服务器的硬件资源。

  ```shell
    [root@localhost ~]# yum install autofs
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    Resolving Dependencies
    --> Running transaction check
    ---> Package autofs.x86_64 1:5.0.7-40.el7 will be installed
    --> Processing Dependency: libhesiod.so.0()(64bit) for package: 1:autofs-5.0.7-40.el7.x86_64
    --> Running transaction check
    ---> Package hesiod.x86_64 0:3.2.1-3.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ===========================================================================================================================================================================================
    Package                                    Arch                                       Version                                             Repository                                 Size
    ===========================================================================================================================================================================================
    Installing:
    autofs                                     x86_64                                     1:5.0.7-40.el7                                      rhel7                                     550 k
    Installing for dependencies:
    hesiod                                     x86_64                                     3.2.1-3.el7                                         rhel7                                      30 k

    Transaction Summary
    ===========================================================================================================================================================================================
    Install  1 Package (+1 Dependent package)

    Total download size: 579 k
    Installed size: 3.6 M
    Is this ok [y/d/N]: y
    Downloading packages:
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    Total                                                                                                                                                       15 MB/s | 579 kB  00:00:00
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
    Installing : hesiod-3.2.1-3.el7.x86_64                                                                                                                                               1/2
    Installing : 1:autofs-5.0.7-40.el7.x86_64                                                                                                                                            2/2
    Verifying  : hesiod-3.2.1-3.el7.x86_64                                                                                                                                               1/2
    Verifying  : 1:autofs-5.0.7-40.el7.x86_64                                                                                                                                            2/2

    Installed:
    autofs.x86_64 1:5.0.7-40.el7

    Dependency Installed:
    hesiod.x86_64 0:3.2.1-3.el7
    Complete!
    [root@localhost ~]#
  ```

* 处于生产环境中的`Linux`服务器，一般会同时管理许多设备的挂载操作。如果把这些设备挂载信息都写入到`autofs`服务的主配置文件中，无疑会让主配置文件臃肿不堪，不利于服务执行效率，也不利于日后修改里面的配置内容，因此在`autofs`服务程序的主配置文件中需要按照“挂载目录 子配置文件”的格式进行填写。挂载目录是设备挂载位置的上一级目录。例如，光盘设备一般挂载到`/media/cdrom`目录中，那么挂载目录写成`/media`即可。对应的子配置文件则是对这个挂载目录内的挂载设备信息作进一步的说明。子配置文件需要用户自行定义，文件名字没有严格要求，但后缀建议以`.misc`结束。