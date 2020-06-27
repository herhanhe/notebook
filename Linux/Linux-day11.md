<!--
 * @Author: herhan
 * @Date: 2020-06-15 19:03:54
 * @LastEditTime: 2020-06-27 16:08:56
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \github\notebook\Linux\Linux-day11.md
--> 
# Linux-day11 使用`Vsftpd`服务传输文件

---
<!-- TOC -->

- [Linux-day11 使用`Vsftpd`服务传输文件](#linux-day11-使用vsftpd服务传输文件)
  - [11.1 文件传输协议（`FTP`）](#111-文件传输协议ftp)
  - [11.2 `Vsftpd`服务程序](#112-vsftpd服务程序)
    - [11.2.1 匿名访问模式](#1121-匿名访问模式)
    - [11.2.2 本地用户模式](#1122-本地用户模式)
    - [11.2.3 虚拟用户模式](#1123-虚拟用户模式)
  - [11.3 `TFTP`简单文件传输协议](#113-tftp简单文件传输协议)

<!-- /TOC -->
## 11.1 文件传输协议（`FTP`）

* `FTP`是一种在互联网中进行文件传输的协议，基于客户端/服务端模式，默认使用20、21号端口：
  * 其中20（数据端口）用于进行数据传输；
  * 端口21（命令端口）用于接受客户端发出的相关`FTP`命令和参数。

* `FTP`服务器普遍部署于内网中，具有容易搭建、方便管理的特点。
* 而且有些`FTP`客户端工具还可以支持文件的多点下载以及断电续传技术。

![11-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/11-1.png)

* `FTP`服务器是按照`FTP`协议在互联网上提供文件存储和访问服务器的主机
* `FTP`客户端则是向服务器发送连接请求，以建立数据传输链路的主机。
* `FTP`协议有下面两种工作模式：
  * 主动模式：`FTP`服务器主动向客户端发起连接请求。
  * 被动模式：`FTP`服务器等待客户端发起连接请求（`FTP`的默认工作模式）
  * 防火墙一般是用于过滤从外网进入内网的流量，因此有些时候需要将`FTP`的工作模式设置为主动模式，才可以传输数据。

* `vsftpd`(`very secure ftp daemon`,非常安全的`FTP`守护进程)是一款运行在`Linux`操作系统上的FTP服务程序，不仅完全开源而且免费，此外，还具有很高的安全性、传输速度，以及支持虚拟用户用户验证等其他`FTP`服务程序不具备的特点。
  * 安装vsftpd服务程序

    ``` shell
        [root@localhost cdrom]# yum install vsftpd
        Loaded plugins: langpacks, product-id, subscription-manager
        This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
        Resolving Dependencies
        --> Running transaction check
        ---> Package vsftpd.x86_64 0:3.0.2-9.el7 will be installed
        --> Finished Dependency Resolution

        Dependencies Resolved

        ====================================================================================
         Package           Arch              Version                 Repository        Size
        ====================================================================================
        Installing:
         vsftpd            x86_64            3.0.2-9.el7             rhel7            166 k

        Transaction Summary
        ====================================================================================
        Install  1 Package

        Total download size: 166 k
        Installed size: 343 k
        Is this ok [y/d/N]: y
        Downloading packages:
        Running transaction check
        Running transaction test
        Transaction test succeeded
        Running transaction
        Installing : vsftpd-3.0.2-9.el7.x86_64                                        1/1 
        Verifying  : vsftpd-3.0.2-9.el7.x86_64                                        1/1 

        Installed:
        vsftpd.x86_64 0:3.0.2-9.el7
    ```

  * `iptables`防火墙管理工具默认禁止了`FTP`传输协议的端口号，因此在正式配置`vsftpd`服务程序之前，为了避免这些默认的防火墙策略“捣乱”，还需要清空`iptables`防火墙的默认策略，并把当前已经被清理的防火墙策略状态保存下来：

    ```shell
        [root@localhost cdrom]# iptables -F
        [root@localhost cdrom]# service iptables save
        iptables: Saving firewall rules to /etc/sysconfig/iptables:[  OK  ]
    ```

  * `vsftpd`服务程序的主配置文件（`/etc/vsftpd/vsftpd.conf`）内容总长度达到123行，但其中大多数参数在开头都添加了井号（#），从而成为注释信息，大家没有必要在注释信息上花费太多的时间。我们可以在`grep`命令后面添加`-v`参数，过滤并反选出没有包含井号（#）的参数行（即过滤掉所有的注释信息），然后将过滤后的参数行通过输出重定向符写回原始的主配置文件中：

    ```shell
        [root@localhost cdrom]# mv /etc/vsftpd/vsftpd.conf /etc/vsftpd/vsftpd.conf.bak
        [root@localhost cdrom]# grep -v "#" /etc/vsftpd/vsftpd.conf.bak > /etc/vsftpd/vsftpd.conf
        [root@localhost cdrom]# cat /etc/vsftpd/vsftpd.conf
        anonymous_enable=YES
        local_enable=YES
        write_enable=YES
        local_umask=022
        dirmessage_enable=YES
        xferlog_enable=YES
        connect_from_port_20=YES
        xferlog_std_format=YES
        listen=NO
        listen_ipv6=YES
        pam_service_name=vsftpd
        userlist_enable=YES
        tcp_wrappers=YES
    ```

  * `vsftpd`服务程序主配置文件中常用的参数以及作用

    参数|作用
    -|-
    `listen=[YES|NO]`|是否以独立运行的方式监听服务
    `listen_address=IP地址`|设置要监听的`IP`地址
    `listen_port=21`|设置`FTP`服务的监听端口
    `download_enable=[YES|NO]`|是否允许下载文件
    `userlist_enable=[YES|NO]`|设置用户列表为“允许”还是“禁止”操作
    `userlist_deny=[YES|NO]`|置用户列表为“允许”还是“禁止”操作
    `max_clients=0`|最大客户连接数，0为不限制
    `max_per_ip=0`|同一`IP`地址的最大连接数，0为不限制
    `anonymous_enable=[YES|NO]`|是否允许匿名用户访问
    `anon_upload_enable=[YES|NO]`|是否允许匿名用户上传文件
    `anon_umask=022`|匿名用户上传文件的`umask`值
    `anon_root=/var/ftp`|匿名用户的`FTP`根目录
    `anon_mkdir_write_enable=[YES|NO]`|是否允许匿名用户创建目录
    `anon_other_write_enable=[YES|NO]`|是否开放匿名用户的其他写入权限（包括重命名、删除等操作权限）
    `anon_max_rate=0`|匿名用户的最大传输速率（字节/秒），0位不限制
    `local_enable=[YES|NO]`|是否允许本地用户登录`FTP`
    `local_umask=022`|本地用户上传文件的`umask`值
    `local_root=/var/ftp`|本地用户的FTP根目录
    `chroot_local_user=[YES|NO]`|是否将用户权限禁锢在`FTP`目录，以确保安全
    `local_max_rate=0`|本地用户最大传输速率（字节/秒），0为不限制

## 11.2 `Vsftpd`服务程序

* `vsftpd`作为更加安全的文件传输的服务程序，允许用户以三种认证模式登录到`FTP`服务器上
  * **匿名开放模式**：是一种最不安全的认证模式，任何人都可以无需密码验证而直接登录到`FTP`服务器。
  * **本地用户模式**：是通过`Linux`系统本地的账户密码信息进行认证的模式，相较于匿名开放模式更安全，而且配置起来也很简单。但是如果被黑客破解了账户的信息，就可以畅通无阻地登录`FTP`服务器，从而完全控制整台服务器
  * **虚拟用户模式**；是三种模式中最安全的一种认证模式，它需要为`FTP`服务单独建立用户数据库文件，虚拟出用来进行口令验证的账户信息，而这些账户信息在服务器系统中实际上是不存在的，仅供`FTP`服务程序进行认证使用。这样，即时黑客破解了账户信息也无法登录服务器，从而有效果降低了破坏范围和影响。
* `ftp`是`Linux`系统中以命令行界面的方式来管理`FTP`传输服务的客户端工具。

  ```shell
    [root@localhost Desktop]# yum install ftp
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    rhel7                                                    | 4.1 kB     00:00
    Resolving Dependencies
    --> Running transaction check
    ---> Package ftp.x86_64 0:0.17-66.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ================================================================================
    Package        Arch              Version                Repository        Size
    ================================================================================
    Installing:
    ftp            x86_64            0.17-66.el7            rhel7             61 k

    Transaction Summary
    ================================================================================
    Install  1 Package

    Total download size: 61 k
    Installed size: 96 k
    Is this ok [y/d/N]: y
    Downloading packages:
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : ftp-0.17-66.el7.x86_64                                       1/1
      Verifying  : ftp-0.17-66.el7.x86_64                                       1/1

    Installed:
      ftp.x86_64 0:0.17-66.el7
  ```

### 11.2.1 匿名访问模式

* 在`vsftpd`服务程序中，匿名开放模式是最不安全的一种认证模式。任何人都可以无需密码验证而直接登录到`FTP`服务器。这种模式一般用来访问不重要的公开文件（在生产环境中尽量不要存放重要文件）。当然，防火墙管理工具（如Tcp_wrappers服务程序）将`vsftpd`服务程序允许访问的主机范围设置为企业内网，也可以提供基本的安全性。

* `vsftpd`服务程序默认开启了匿名开放模式，我们需要做的就是开放匿名用户的上传、下载文件的权限，以及让匿名用户创建、删除、更名文件的权限。需要注意的是，针对匿名用户放开这些权限会带来潜在危险，我们只是为了在`Linux`系统中练习配置`vsftpd`服务程序而放开了这些权限，不建议在生产环境中如此行事。

* 可以向匿名用户开放的权限参数以及作用
  参数|作用
  -|-
  `anonymous_enable`=`YES`|允许匿名访问模式
  `anon_umask`=022|匿名用户上传文件的`umask`值
  `anon_upload_enable`=`YES`|允许匿名用户上传文件
  `anon_mkdir_write_enable`=`YES`|允许匿名用户创建目录
  `anon_other_write_enable`=`YES`|允许匿名用户修改目录名称或删除目录

* 试验`vsftpd`匿名访问模式配置

  * 1.`vsftpd`配置文件，参数配置

    ```shell
      [root@localhost Desktop]# vim /etc/vsftpd/vsftpd.conf
      anonymous_enable=YES
      anon_umask=022
      anon_upload_enable=YES
      anon_mkdir_write_enable=YES
      anon_othre_write_enable=YES

      local_enable=YES
      write_enable=YES
      local_umask=022
      dirmessage_enable=YES
      xferlog_enable=YES
      connect_from_port_20=YES
      xferlog_std_format=YES
      listen=NO
      listen_ipv6=YES
      pam_service_name=vsftpd
      userlist_enable=YES
      tcp_wrappers=YES
    ```

  * 2.在`vsftpd`服务程序的主配置文件中正确填写参数，然后保存并退出。还需要重启`vsftpd`服务程序，让新的配置参数生效。

    ```shell
      [root@localhost Desktop]# systemctl restart vsftpd
      [root@localhost Desktop]# systemctl enable vsftpd
      ln -s '/usr/lib/systemd/system/vsftpd.service' '/etc/systemd/system/multi-user.target.wants/vsftpd.service'
    ```

  * 3.可以在客户端执行ftp命令连接到远程的`FTP`服务器了。在`vsftpd`服务程序的匿名开放认证模式下，其账户统一为`anonymous`,密码为空。而且

    ```shell
      [root@localhost Desktop]# ftp 192.168.10.10
      Connected to 192.168.10.10 (192.168.10.10).
      220 (vsFTPd 3.0.2)
      Name (192.168.10.10:root): anonymous
      331 Please specify the password.
      Password:
      230 Login successful.
      Remote system type is UNIX.
      Using binary mode to transfer files.
      ftp> cd pub
      250 Directory successfully changed.
      ftp> mkdir files
      550 Create directory operation failed.
    ```
  
  * 4.系统显示拒绝创建目录！我们明明在前面清空了iptables防火墙策略，而且也在`vsftpd`服务程序的主配置文件中添加了允许匿名用户创建目录和写入文件的权限
    * 在`vsftpd`服务程序的匿名开放认证模式下，默认访问的是/var/ftp目录。查看该目录的权限得知，只有`root`管理员才有写入权限。下面将目录的所有者身份改成系统账户`ftp`即可（该账户在系统中已经存在）

      ```shell
        [root@localhost Desktop]# ls -ld /var/ftp/pub
        drwxr-xr-x. 2 root root 6 Mar  7  2014 /var/ftp/pub
        [root@localhost Desktop]# chown -Rf ftp /var/ftp/pub
        [root@localhost Desktop]# ls -ld /var/ftp/pub
        drwxr-xr-x. 2 ftp root 6 Mar  7  2014 /var/ftp/pub

        [root@localhost Desktop]# ftp 192.168.10.10
        Connected to 192.168.10.10 (192.168.10.10).
        220 (vsFTPd 3.0.2)
        Name (192.168.10.10:root): anonymous
        331 Please specify the password.
        Password:
        230 Login successful.
        Remote system type is UNIX.
        Using binary mode to transfer files.
        ftp> cd pub
        250 Directory successfully changed.
        ftp> mkdir files
        550 Create directory operation failed.
      ```

    * 系统再次报错！尽管我们在使用`ftp`命令登入`FTP`服务器后，再创建目录时系统依然提示操作失败，但是报错信息却发生了变化。在没有写入权限时，系统提示“权限拒绝”（`Permission denied`）。但现在系统提示“创建目录的操作失败”（`Create directory operation failed`），想必各位读者也应该意识到是`SELinux`服务在“捣乱”了吧。
    * 下面使用`getsebool`命令查看与`FTP`相关的`SELinux`域策略都有哪些：

      ```shell
        [root@localhost Desktop]# getsebool -a | grep ftp
        ftp_home_dir --> off
        ftpd_anon_write --> off
        ftpd_connect_all_unreserved --> off
        ftpd_connect_db --> off
        ftpd_full_access --> off
        ftpd_use_cifs --> off
        ftpd_use_fusefs --> off
        ftpd_use_nfs --> off
        ftpd_use_passive_mode --> off
        httpd_can_connect_ftp --> off
        httpd_enable_ftp_server --> off
        sftpd_anon_write --> off
        sftpd_enable_homedirs --> off
        sftpd_full_access --> off
        sftpd_write_ssh_home --> off
        tftp_anon_write --> off
        tftp_home_dir --> off
      ```

    * 根据经验（需要长期培养，别无它法）和策略的名称判断出是`ftpd_full_access--> off`策略规则导致了操作失败。接下来修改该策略规则，并且在设置时使用`-P`参数让修改过的策略永久生效，确保在服务器重启后依然能够顺利写入文件。

      ```shell
        [root@localhost Desktop]# setsebool -P ftpd_full_access=on
      ```

    * 现在便可以顺利执行文件创建、修改及删除等操作了

      ```shell
        [root@localhost Desktop]# ftp 192.168.10.10
        Connected to 192.168.10.10 (192.168.10.10).
        220 (vsFTPd 3.0.2)
        Name (192.168.10.10:root): anonymous
        331 Please specify the password.
        Password:
        230 Login successful.
        Remote system type is UNIX.
        Using binary mode to transfer files.
        ftp> cd pud
        550 Failed to change directory.
        ftp> cd pub
        250 Directory successfully changed.
        ftp> mkdir files
        257 "/pub/files" created
        ftp> rename files database
        350 Ready for RNTO.
        250 Rename successful.
        ftp> rmdir database
        250 Remove directory operation successful.
        ftp> exit
        221 Goodbye.
      ```

### 11.2.2 本地用户模式

* 相较于匿名开放模式，本地用户模式要更安全，而且配置起来也很简单。如果大家之前用的是匿名开放模式，现在就可以将它关了，然后开启本地用户模式。
* 本地用户模式使用的权限参数以及作用

  参数|作用
  -|-
  `anonymous_enable`=`NO`|禁止匿名访问模式
  `local_enable`=`YES`|允许本地用户模式
  `write_enable`=`YES`|设置可写权限
  `local_umask`=022|本地用户模式创建文件的`umask`值
  `userlist_deny`=`YES`|启用“禁止用户名单”，名单文件为`ftpusers`和`user_list`
  `userlist_enable`=`YES`|开启用户作用名单文件功能
  
* 试验`vsftpd`本地用户模式配置

  * 1.`vsftpd`配置文件，参数配置
  
    ```shell
      [root@localhost Desktop]# vim /etc/vsftpd/vsftpd.conf
      anonymous_enable=NO
      local_enable=YES
      write_enable=YES
      local_umask=022
      dirmessage_enable=YES
      xferlog_enable=YES
      connect_from_port_20=YES
      xferlog_std_format=YES
      listen=NO
      listen_ipv6=YES
      pam_service_name=vsftpd
      userlist_enable=YES
      tcp_wrappers=YES
    ```
  
  * 2.在`vsftpd`服务程序的主配置文件中正确填写参数，然后保存并退出。还需要重启`vsftpd`服务程序，让新的配置参数生效

    ```shell
      [root@localhost Desktop]# systemctl restart vsftpd
      [root@localhost Desktop]# systemctl enable vsftpd
      ln -s '/usr/lib/systemd/system/vsftpd.service' '/etc/systemd/system/multi-user.target.wants/vsftpd.service
    ```
  
  * 3.按理来讲，现在已经完全可以本地用户的身份登录`FTP`服务器了。但是在使用`root`管理员登录后，系统提示如下的错误信息：

    ```shell
      [root@localhost Desktop]# ftp 192.168.10.10
      Connected to 192.168.10.10 (192.168.10.10).
      220 (vsFTPd 3.0.2)
      Name (192.168.10.10:root): root
      530 Permission denied.
      Login failed.
      ftp>
    ```

  * 4.可见，在我们输入`root`管理员的密码之前，就已经被系统拒绝访问了。这是因为`vsftpd`服务程序所在的目录中默认存放着两个名为“用户名单”的文件（`ftpusers`和`user_list`）。不知道大家是否已看过一部日本电影“死亡笔记”（刘遄老师在上学期间的最爱），里面就提到有一个黑色封皮的小本子，只要将别人的名字写进去，这人就会挂掉。

    ```shell
      [root@localhost Desktop]# cat /etc/vsftpd/user_list 
      # vsftpd userlist
      # If userlist_deny=NO, only allow users in this file
      # If userlist_deny=YES (default), never allow users in this file, and
      # do not even prompt for a password.
      # Note that the default vsftpd pam config also checks /etc/vsftpd/ftpusers
      # for users that are denied.
      root 删除
      bin
      daemon
      adm
      lp
      sync
      shutdown
      halt
      mail
      news
      uucp
      operator
      games
      nobody
      [root@localhost Desktop]# cat /etc/vsftpd/ftpusers
      # Users that are not allowed to login via ftp
      root 删除
      bin
      daemon
      adm
      lp
      sync
      shutdown
      halt
      mail
      news
      uucp
      operator
      games
      nobody
    ```

  * 5.`vsftpd`服务程序为了保证服务器的安全性而默认禁止了`root`管理员和大多数系统用户的登录行为，这样可以有效地避免黑客通过`FTP`服务对`root`管理员密码进行暴力破解。如果您确认在生产环境中使用`root`管理员不会对系统安全产生影响，只需按照上面的提示删除掉`root`用户名即可。我们也可以选择`ftpusers`和`user_list`文件中没有的一个普通用户尝试登录`FTP`服务器：

    ```shell
      [root@localhost Desktop]# ftp 192.168.10.10
      Connected to 192.168.10.10 (192.168.10.10).
      220 (vsFTPd 3.0.2)
      Name (192.168.10.10:root): herhan
      331 Please specify the password.
      Password:
      230 Login successful.
      Remote system type is UNIX.
      Using binary mode to transfer files.
      ftp> mkdir files
      550 Create directory operation failed.
    ```

  * 6.在采用本地用户模式登录`FTP`服务器后，默认访问的是该用户的家目录，也就是说，访问的是`/home/linuxprobe`目录。而且该目录的默认所有者、所属组都是该用户自己，因此不存在写入权限不足的情况。但是当前的操作仍然被拒绝，是因为我们刚才将虚拟机系统还原到最初的状态了。为此，需要再次开启`SELinux`域中对`FTP`服务的允许策略：

    ```shell
      [root@localhost Desktop]# getsebool -a | grep ftp
      ftp_home_dir --> off
      ftpd_anon_write --> off
      ftpd_connect_all_unreserved --> off
      ftpd_connect_db --> off
      ftpd_full_access --> off
      ftpd_use_cifs --> off
      ftpd_use_fusefs --> off
      ftpd_use_nfs --> off
      ftpd_use_passive_mode --> off
      httpd_can_connect_ftp --> off
      httpd_enable_ftp_server --> off
      sftpd_anon_write --> off
      sftpd_enable_homedirs --> off
      sftpd_full_access --> off
      sftpd_write_ssh_home --> off
      tftp_anon_write --> off
      tftp_home_dir --> off
      [root@localhost Desktop]# setsebool -P ftpd_full_access=on
    ```

  * 在实验课程和生产环境中设置`SELinux`域策略时，一定记得添加`-P`参数，否则服务器在重启后就会按照原有的策略进行控制，从而导致配置过的服务无法使用。  

  * 7.在配置妥当后再使用本地用户尝试登录下`FTP`服务器，分别执行文件的创建、重命名及删除等命令。操作均成功

    ```shell
      [root@localhost Desktop]# ftp 192.168.10.10
      Connected to 192.168.10.10 (192.168.10.10).
      220 (vsFTPd 3.0.2)
      Name (192.168.10.10:root): herhan
      331 Please specify the password.
      Password:
      230 Login successful.
      Remote system type is UNIX.
      Using binary mode to transfer files.
      ftp> mkdir files
      257 "/home/linuxprobe/files" created
      ftp> rename files database
      350 Ready for RNTO.
      250 Rename successful.
      ftp> rmdir database
      250 Remove directory operation successful.
      ftp> exit
      221 Goodbye.
    ```

### 11.2.3 虚拟用户模式

* 虚拟用户模式是这三种模式中最安全的一种认证模式，当然，因为安全性较之于前面两种模式有了提升，所以配置流程也会稍微复杂一些。

* 1.创建用于进行`FTP`认证的用户数据库文件，其中奇数行为账户名，偶数行为密码

  ```shell
    [root@localhost Desktop]# cd /etc/vsftpd/
    [root@localhost vsftpd]# vim vuser.list
    lilie
    123
    hanmeimei
    123
  ```

* 但是，明文信息既不安全，也不符合让`vsftpd`服务程序直接加载的格式，因此需要使用`db_load`命令用哈希（`hash`）算法将原始的明文信息文件转换成数据库文件，并且降低数据库文件的权限（避免其他人看到数据库文件的内容），然后再把原始的明文信息文件删除。

  ```shell
    [root@localhost vsftpd]# db_load -T -t hash -f vuser.list vuser.db
    [root@localhost vsftpd]# file vuser.db
    vuser.db: Berkeley DB (Hash, version 9, native byte-order)
    [root@localhost vsftpd]# chmod 600 vuser.db
    [root@localhost vsftpd]# rm -rf vuser.list
  ```

* 2.创建`vsftpd`服务程序用于存储文件的根目录以及虚拟用户映射的系统本地用户。`FTP`服务用于存储文件的根目录指的是，当虚拟用户登录后所访问的默认位置。

* 由于`Linux`系统中的每一个文件都有所有者、所属组属性，例如使用虚拟账户新建了一个文件，但是系统中找不到账户，就会导致这个文件的权限出现错误。为此，需要再创建一个可以映射到虚拟用户的系统本地用户。简单来说，就是让虚拟用户默认登录到与之有映射关系的这个系统本地用户的家目录中，虚拟用户创建的文件的属性也都归属于这个系统本地用户，从而避免Linux系统无法处理虚拟用户所创建文件的属性权限
* 为了方便管理`FTP`服务器上的数据，可以把这个系统本地用户的家目录设置为`/var`目录（该目录用来存放经常发生改变的数据）。并且为了安全起见，我们将这个系统本地用户设置为不允许登录`FTP`服务器，这不会影响虚拟用户登录，而且还可以避免黑客通过这个系统本地用户进行登录。

  ```shell
    [root@localhost ~]# useradd -d /var/ftproot -s /sbin/nologin virtual
    [root@localhost ~]# ls -ld /var/ftproot
    drwx------. 3 virtual virtual 74 Jun 22 23:52 /var/ftproot
    [root@localhost ~]# chmod -Rf 755 /var/ftproot
  ```

* 3.建立用于支持虚拟用户的`PAM`文件

* `PAM`（可插拔认证模块）是一种认证机制，通过一些动态链接库和统一的`API`把系统提供的服务与认证方式分开，使得系统管理员可以根据需求灵活调整服务程序的不同认证方式。
* `PAM`是一组安全机制的模块，系统管理员可以用来轻易地调整服务程序的认证方式，而不必对应用程序进行任何修改。`PAM`采取了分层设计（应用程序层、应用接口层、鉴别模块层）的思想，其结构如图11-2所示。

![11-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/11-2.png)

* 新建一个用于虚拟用户认证的`PAM`文件`vsftpd.vu`,其中`PAM`文件内的`db=`参数为使用`db_load`命令生成的账户密码数据库文件的路径，但不用写数据库文件的后缀：

  ```shell
    [root@localhost ~]# vim /etc/pam.d/vsftpd.vu
    auth    required        pam_userdb.so db=/etc/vsftpd/vuser
    account required        pam_userdb.so db=/etc/vsftpd/vuser
  ```

* 4.在`vsftpd`服务程序的主配置文件中通过`pam_service_name`参数将`PAM`认证文件的名称修改为`vsftpd.vu`，`PAM`作为应用程序层与鉴别模块层的连接纽带，可以让应用程序根据需求灵活地在自身插入所需的鉴别功能模块。当应用程序需要`PAM`认证时，则需要在应用程序中定义负责认证的`PAM`配置文件，实现所需的认证功能。

* 利用`PAM`文件进行认证时使用的参数以及作用

  参数|作用
  -|-
  `anonymous_enable`=`NO`|禁止匿名开放模式
  `local_enable`=`YES`|允许本地用户模式
  `guest_enable`=`YES`|开启虚拟用户模式
  `guest_username`=`virtual`|指定虚拟用户账户
  `pam_service_name`=`vsftpd.vu`|指定`PAM`文件
  `allow_writeable_chroot`=`YES`|允许对禁锢的`FTP`根目录执行写入操作，而且不拒绝用户的登录请求

  ```shell
    [root@localhost ~]# vim /etc/vsftpd/vsftpd.conf
    anonymous_enable=NO
    local_enable=YES
    guset_enable=YES
    guest_username=virual
    allow_writeable_chroot=YES
    write_enable=YES
    local_umask=022
    dirmessage_enable=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    xferlog_std_format=YES
    listen=NO
    listen_ipv6=YES
    pam_service_name=vsftpd.vu
    userlist_enable=YES
    tcp_wrappers=YES
  ```

* 5.为虚拟用户设置不同的权限。
* 虽然账户`lilie`和`hanmeimei`都是用于`vsftpd`服务程序认证的虚拟账户，但是我们依然想对这两人进行区别对待。比如，允许李磊上传、创建、修改、查看、删除文件，只允许韩梅梅查看文件。这可以通过`vsftpd`服务程序来实现。只需新建一个目录，在里面分别创建两个以`lilie`和`hanmeimei`命名的文件，其中在名为`lilie`的文件中写入允许的相关权限（使用匿名用户的参数）：

  ```shell
    [root@localhost ~]# mkdir /etc/vsftpd/vusers_dir
    [root@localhost ~]# cd /etc/vsftpd/vusers_dir/
    [root@localhost vusers_dir]# touch hanmeimei
    [root@localhost vusers_dir]# vim lilie
    anon_upload_enable=YES
    anon_mkdir_write_enable=YES
    anon_other_write_enable=YES
  ```

* 然后再次修改`vsftpd`主配置文件，通过添加`user_config_dir`参数来定义这两个虚拟用户不同权限的配置文件所存放的路径。为了让修改后的参数立即生效，需要重启`vsftpd`服务程序并将该服务添加到开机启动项中：

  ```shell
    [root@localhost vusers_dir]# vim /etc/vsftpd/vsftpd.conf
    anonymous_enable=NO
    local_enable=YES
    guest_enable=YES
    guest_username=virtual
    allow_writeable_chroot=YES
    write_enable=YES
    local_umask=022
    dirmessage_enable=YES
    xferlog_enable=YES
    connect_from_port_20=YES
    xferlog_std_format=YES
    listen=NO
    listen_ipv6=YES
    pam_service_name=vsftpd.vu
    userlist_enable=YES
    tcp_wrappers=YES
    user_config_dir=/etc/vsftpd/vusers_dir
    [root@localhost vusers_dir]# systemctl restart vsftpd
  ```

* 6.设置`SELinux`域允许策略，然后使用虚拟用户模式登录FTP服务器。相信大家可以猜到，`SELinux`会继续来捣乱。所以，先按照前面实验中的步骤开启`SELinux`域的允许策略，以免再次出现操作失败的情况：

  ```shell
    [root@localhost Desktop]# getsebool -a | grep ftp
    ftp_home_dir --> off
    ftpd_anon_write --> off
    ftpd_connect_all_unreserved --> off
    ftpd_connect_db --> off
    ftpd_full_access --> off
    ftpd_use_cifs --> off
    ftpd_use_fusefs --> off
    ftpd_use_nfs --> off
    ftpd_use_passive_mode --> off
    httpd_can_connect_ftp --> off
    httpd_enable_ftp_server --> off
    sftpd_anon_write --> off
    sftpd_enable_homedirs --> off
    sftpd_full_access --> off
    sftpd_write_ssh_home --> off
    tftp_anon_write --> off
    tftp_home_dir --> off
    [root@localhost Desktop]# setsebool -P ftpd_full_access=on
  ```
  
  ```shell
    [root@localhost Desktop]# ftp 192.168.10.10
    Connected to 192.168.10.10 (192.168.10.10).
    220 (vsFTPd 3.0.2)
    Name (192.168.10.10:root): hanmeimei
    331 Please specify the password.
    Password:
    230 Login successful.
    Remote system type is UNIX.
    Using binary mode to transfer files.
    ftp> mkdir files
    550 Permission denied.
    ftp> exit
    221 Goodbye.
    [root@localhost Desktop]# ftp 192.168.10.10
    Connected to 192.168.10.10 (192.168.10.10).
    220 (vsFTPd 3.0.2)
    Name (192.168.10.10:root): lilie
    331 Please specify the password.
    Password:
    230 Login successful.
    Remote system type is UNIX.
    Using binary mode to transfer files.
    ftp> mkdir files
    257 "/files" created
    ftp> rename files database
    350 Ready for RNTO.
    250 Rename successful.
    ftp> rmdir database
    250 Remove directory operation successful.
    ftp> exit
    221 Goodbye.
  ```

## 11.3 `TFTP`简单文件传输协议

* 简单文件传输协议（`Trivial File Transfer Protocol，TFTP`）是一种基于`UDP`协议在客户端和服务器之间进行简单文件传输的协议。顾名思义，它提供不复杂、开销不大的文件传输服务（可将其当作`FTP`协议的简化版本）。

* `TFTP`的命令功能不如`FTP`服务强大，甚至不能遍历目录，在安全性方面也弱于`FTP`服务。而且，由于`TFTP`在传输文件时采用的是`UDP`协议，占用的端口号为`69`，因此文件的传输过程也不像`FTP`协议那样可靠。但是，因为`TFTP`不需要客户端的权限认证，也就减少了无谓的系统和网络带宽消耗，因此在传输琐碎（`trivial`）不大的文件时，效率更高。

  ```shell
    [root@localhost ~]# yum install tftp-server tftp
    Loaded plugins: langpacks, product-id, subscription-manager
    This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
    rhel7                                                                                                                                                               | 4.1 kB  00:00:00     
    Resolving Dependencies
    --> Running transaction check
    ---> Package tftp.x86_64 0:5.2-11.el7 will be installed
    ---> Package tftp-server.x86_64 0:5.2-11.el7 will be installed
    --> Processing Dependency: xinetd for package: tftp-server-5.2-11.el7.x86_64
    --> Running transaction check
    ---> Package xinetd.x86_64 2:2.3.15-12.el7 will be installed
    --> Finished Dependency Resolution

    Dependencies Resolved

    ===========================================================================================================================================================================================
    Package                                       Arch                                     Version                                              Repository                               Size
    ===========================================================================================================================================================================================
    Installing:
    tftp                                          x86_64                                   5.2-11.el7                                           rhel7                                    35 k
    tftp-server                                   x86_64                                   5.2-11.el7                                           rhel7                                    44 k
    Installing for dependencies:
    xinetd                                        x86_64                                   2:2.3.15-12.el7                                      rhel7                                   128 k

    Transaction Summary
    ===========================================================================================================================================================================================
    Install  2 Packages (+1 Dependent package)

    Total download size: 207 k
    Installed size: 373 k
    Is this ok [y/d/N]: y
    Downloading packages:
    -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------
    Total                                                                                                                                                      787 kB/s | 207 kB  00:00:00
    Running transaction check
    Running transaction test
    Transaction test succeeded
    Running transaction
      Installing : 2:xinetd-2.3.15-12.el7.x86_64                                                                                                                                           1/3
      Installing : tftp-server-5.2-11.el7.x86_64                                                                                                                                           2/3
      Installing : tftp-5.2-11.el7.x86_64                                                                                                                                                  3/3
      Verifying  : 2:xinetd-2.3.15-12.el7.x86_64                                                                                                                                           1/3
      Verifying  : tftp-5.2-11.el7.x86_64                                                                                                                                                  2/3
      Verifying  : tftp-server-5.2-11.el7.x86_64                                                                                                                                           3/3

    Installed:
      tftp.x86_64 0:5.2-11.el7                                                                 tftp-server.x86_64 0:5.2-11.el7

    Dependency Installed:
      xinetd.x86_64 2:2.3.15-12.el7

    Complete!
  ```

* 1.在`RHEL 7`系统中，`TFTP`服务是使用`xinetd`服务程序来管理的。`xinetd`服务可以用来管理多种轻量级的网络服务，而且具有强大的日志功能。简单来说，在安装`TFTP`软件包后，还需要在`xinetd`服务程序中将其开启，把默认的禁用（`disable`）参数修改为`no`：

  ```shell
    [root@localhost ~]# vim /etc/xinetd.d/tftp
    # default: off
    # description: The tftp server serves files using the trivial file transfer \
    #       protocol.  The tftp protocol is often used to boot diskless \
    #       workstations, download configuration files to network-aware printers, \
    #       and to start the installation process for some operating systems.
    service tftp
    {
            socket_type             = dgram
            protocol                = udp
            wait                    = yes
            user                    = root
            server                  = /usr/sbin/in.tftpd
            server_args             = -s /var/lib/tftpboot
            disable                 = no
            per_source              = 11
            cps                     = 100 2
            flags                   = IPv4
    }
  ```

* 2.然后，重启`xinetd`服务并将它添加到系统的开机启动项中，以确保`TFTP`服务在系统重启后依然处于运行状态。考虑到有些系统的防火墙默认没有允许`UDP`协议的`69`端口，因此需要手动将该端口号加入到防火墙的允许策略中：

  ```shell
    [root@localhost ~]# systemctl restart xinetd
    [root@localhost ~]# systemctl enable xinetd
    [root@localhost ~]# firewall-cmd --permanent --add-port=69/udp
    success
    [root@localhost ~]# firewall-cmd --reload
    success
  ```

* 3.`TFTP`的根目录为`/var/lib/tftpboot`。我们可以使用刚安装好的`tftp`命令尝试访问其中的文件，亲身体验`TFTP`服务的文件传输过程。在使用`tftp`命令访问文件时，可能会用到表11-5中的参数。

* `tftp`命令中可用的参数以及作用

  命令|作用
  -|-
  `?`|帮助信息
  `put`|上传文件
  `get`|下载文件
  `verbose`|显示详细的处理信息
  `status`|显示当前的状态信息
  `binary`|使用二进制进行传输
  `ascii`|使用`ASCII`码进行传输
  `timeout`|设置重传的超时时间
  `quit`|退出

  ```shell
    [root@localhost ~]# echo "i love linux" > /var/lib/tftpboot/readme.txt
    [root@localhost ~]# tftp 192.168.10.10
    tftp> get readme.txt
    tftp> quit
    [root@localhost ~]# ls
    anaconda-ks.cfg  Desktop  Documents  Downloads  initial-setup-ks.cfg  Music  Pictures  Public  readme.txt  Templates  Videos
    [root@localhost ~]# cat readme.txt
    i love linux
  ```