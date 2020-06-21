# Linux-day11 使用`Vsftpd`服务传输文件

---

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
  