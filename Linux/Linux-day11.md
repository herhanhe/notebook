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



