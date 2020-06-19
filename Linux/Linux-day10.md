
# Linux-day10 使用`Apache`服务部署静态网站

---
- [Linux-day10 使用`Apache`服务部署静态网站](#linux-day10-使用apache服务部署静态网站)
  - [1.网站服务程序](#1网站服务程序)
  - [2.配置服务文件参数](#2配置服务文件参数)
  - [3 `SELinux`安全子系统](#3-selinux安全子系统)
  - [4.个人用户主页功能](#4个人用户主页功能)
  - [5.虚拟网站主机功能](#5虚拟网站主机功能)
    - [5.1 基于ip地址](#51-基于ip地址)
    - [5.2 基于主机域名](#52-基于主机域名)
    - [5.3 基于端口号](#53-基于端口号)
  - [6.`Apache`的访问控制](#6apache的访问控制)

## 1.网站服务程序

* 1970年，作为互联网前身的`ARPANET`（阿帕网）已初具雏形，并开始向非军用部门开放，许多大学和商业部门开始接入。虽然彼时阿帕网的规模（只有4台主机联网运行）还不如现在的局域网成熟，但是它依然为网络技术的进步打下了扎实的基础。
* `Web`网络服务是一种被动访问的服务程序，即只有接收到互联网中其他主机发出的请求后才会响应，最终用于提供服务程序的`Web`服务器会通过`HTTP`（超文本传输协议）或`HTTPS`（安全超文本传输协议）把请求的内容传送给用户。

    ![10-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586869988213.png)

* 目前能够提供`web`网络服务的程序有`IIS、Nginx和Apache`
  * `IIS(Internet Information Services,互联网信息服务)` 是`Windows`系统中默认的`web`服务程序，这是一款图形化的网站管理工具，不仅可以提供`web`网站服务，还可以提供`FTP、NMTP、SMTP`等服务。
  * `Nginx`程序作为一款轻量级的网站服务软件，因其稳定性和丰富的功能而快速占领服务器市场，但`Nginx`最被认可的还当是系统资源消耗低且并发能力强
  * `Apache`程序是目前拥有很高市场占有率的`Web`服务程序之一，其跨平台和安全性广泛被认可且拥有快速、可靠、简单的`API`扩展。
  * `Apache`服务程序可以运行在`Linux`系统、`UNIX`系统甚至是`Windows`系统中，支持基于`IP`、域名及端口号的虚拟主机功能，支持多种认证方式，集成有代理服务器模块、安全`Socket`层（`SSL`），能够实时监视服务状态与定制日志消息，并有着各类丰富的模块支持。

* `Apache`服务部署
  * 1.把光盘设备中的系统镜像挂载到`/media/cdrom`目录

    ```shell
      [root@localhost Desktop]# mkdir -p /media/cdrom
      [root@localhost Desktop]# mount /dev/cdrom /media/cdrom
      mount: /dev/sr0 is write-protected, mounting read-only
    ```

  * 2.使用`Vim`文本编辑器创建`Yum`仓库的配置文件

    ```shell
      [root@localhost Desktop]# vim /etc/yum.repos.d/rhel7.repo
      [rhel7]
      name=rhel7
      baseurl=file:///media/cdrom
      enable=1
      gpgcheck=0
      [root@localhost Desktop]# yum makecache
      Loaded plugins: langpacks, product-id, subscription-manager
      This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
      rhel7                         | 4.1 kB     00:00
      (1/5): rhel7/group_gz         | 134 kB     00:00
      (2/5): rhel7/primary_db       | 3.4 MB     00:00
      (3/5): rhel7/filelists_db     | 3.0 MB     00:00
      (4/5): rhel7/other_db         | 1.3 MB     00:00
      (5/5): rhel7/productid        | 1.6 kB     00:00
      Metadata Cache Created
    ```

  * 3.安装`Apache`服务程序。

    ```shell
      [root@localhost Desktop]# yum install httpd
      Loaded plugins: langpacks, product-id, subscription-manager
      This system is not registered to Red Hat Subscription Management. You can use subscription-manager to register.
      Resolving Dependencies
      --> Running transaction check
      ---> Package httpd.x86_64 0:2.4.6-17.el7 will be installed
      --> Processing Dependency: httpd-tools = 2.4.6-17.el7 for package: httpd-2.4.6-17.el7.x86_64
      --> Processing Dependency: /etc/mime.types for package: httpd-2.4.6-17.el7.x86_64
      --> Processing Dependency: libapr-1.so.0()(64bit) for package: httpd-2.4.6-17.el7.x86_64
      --> Processing Dependency: libaprutil-1.so.0()(64bit) for package: httpd-2.4.6-17.el7.x86_64
      --> Running transaction check
      ---> Package apr.x86_64 0:1.4.8-3.el7 will be installed
      ---> Package apr-util.x86_64 0:1.5.2-6.el7 will be installed
      ---> Package httpd-tools.x86_64 0:2.4.6-17.el7 will be installed
      ---> Package mailcap.noarch 0:2.1.41-2.el7 will be installed
      --> Finished Dependency Resolution

      Dependencies Resolved

      ================================================================================
      Package             Arch           Version                 Repository     Size
      ================================================================================
      Installing:
      httpd               x86_64         2.4.6-17.el7            rhel7         1.2 M
      Installing for dependencies:
      apr                 x86_64         1.4.8-3.el7             rhel7         103 k
      apr-util            x86_64         1.5.2-6.el7             rhel7          92 k
      httpd-tools         x86_64         2.4.6-17.el7            rhel7          77 k
      mailcap             noarch         2.1.41-2.el7            rhel7          31 k

      Transaction Summary
      ================================================================================
      Install  1 Package (+4 Dependent packages)

      Total download size: 1.5 M
      Installed size: 4.3 M
      Is this ok [y/d/N]: y
      Downloading packages:
      --------------------------------------------------------------------------------
      Total                                              5.3 MB/s | 1.5 MB  00:00
      Running transaction check
      Running transaction test
      Transaction test succeeded
      Running transaction
      Installing : apr-1.4.8-3.el7.x86_64                                       1/5
      Installing : apr-util-1.5.2-6.el7.x86_64                                  2/5
      Installing : httpd-tools-2.4.6-17.el7.x86_64                              3/5
      Installing : mailcap-2.1.41-2.el7.noarch                                  4/5
      Installing : httpd-2.4.6-17.el7.x86_64                                    5/5
      Verifying  : mailcap-2.1.41-2.el7.noarch                                  1/5
      Verifying  : httpd-tools-2.4.6-17.el7.x86_64                              2/5
      Verifying  : apr-1.4.8-3.el7.x86_64                                       3/5
      Verifying  : apr-util-1.5.2-6.el7.x86_64                                  4/5
      Verifying  : httpd-2.4.6-17.el7.x86_64                                    5/5

      Installed:
      httpd.x86_64 0:2.4.6-17.el7

      Dependency Installed:
      apr.x86_64 0:1.4.8-3.el7                 apr-util.x86_64 0:1.5.2-6.el7
      httpd-tools.x86_64 0:2.4.6-17.el7        mailcap.noarch 0:2.1.41-2.el7

      Complete!
    ```

  * 4.启动`httpd`服务程序并将其加入到开机启动项中，使其能够随系统开机而运行，从而持续为用户提供web服务

    ```shell
      [root@localhost Desktop]# systemctl start httpd
      [root@localhost Desktop]# systemctl enable httpd
      ln -s '/usr/lib/systemd/system/httpd.service' '/etc/systemd/system/multi-user.target.wants/httpd.service'
    ```

  * 5.通过`firefox`浏览器访问`http://127.0.0.1`，就可以看到httpd服务程序的默认页面

    ```shell
      [root@localhost Desktop]# firefox
    ```

    ![10-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586874756649.png)

## 2.配置服务文件参数

* 在Linux系统中的配置文件

    名称|地址
    -|-
    服务目录|`/etc/httpd`
    主配置文件|`/etc/httpd/conf/httpd.conf`
    网站数据目录|`/var/www/html`
    访问日志|`/var/log/httpd/access_log`
    错误日志|`/var/log/httpd/error_log`

* 在`httpd`服务程序的主配置文件，存在三种类型的信息：注释行信息、全局配置、区域配置

    ![10-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1586875459739.png)
  * 全局配置参数就是一种全局性的配置参数，可作用于对所有的子站点，既保证了子站点的正常访问，也有效减少了频繁写入重复参数的工作量。
  * 区域配置参数则是单独针对于每个独立的子站点进行设置的。

* 在`httpd`服务程序主配置文件中，最为常用的参数：

    参数|用途描述
    -|-
    `ServerRoot`|服务目录
    `ServerAdmin`|管理员邮箱
    `User`|运行服务的用户
    `Group`|运行服务的用户组
    `ServerName`|网站服务器的域名
    `DcoumentRoot`|网站数据目录
    `Listen`|监听的`ip`地址与端口号
    `DirectoryIndex`|默认的索引页页面
    `ErrorLog`|错误日志文件
    `CustomLog`|访问日志文件
    `Timeout`|网页超时时间，默认为300秒

* `DocumentRoot`参数用于定义网站数据的保存路径，其参数的默认值是把网站数据存放到`/var/www/html`目录中，而当前网站普遍的首页面名称是`index.html`；

* 范例
  * 更改`/var/www/html`目录中`index.html`,替换掉`httpd`服务程序的默认首页面，该操作会立即生效。
  
    ```shell
      [root@localhost Desktop]# echo "Welcome To herhan.com" > /var/www/html/index.html
      [root@localhost Desktop]# firefox
    ```

    ![10-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587213354074.png)

  * 更改网站数据的目录为`/home/wwwroot`
    * 1.建立网站数据的保存目录，并创建首页文件。

      ```shell
        [root@localhost Desktop]# mkdir /home/wwwroot
        [root@localhost Desktop]# echo "The New Web Directory" > /home/wwwroot/index.html
      ```

    * 2.打开`httpd`服务程序的主配置文件，将约第119行用于定义网站数据保存路径的参数`DocumentRoot`修改为`/home/wwwroot`,同时还需要将约第124行用于定义目录权限的参数`Directory`后面的路径也修改为`/home/wwwroot`。配置文件修改完毕后即可保存并退出。

      ```shell
        114 #
        115 # DocumentRoot: The directory out of which you will serve your
        116 # documents. By default, all requests are taken from this directory, but
        117 # symbolic links and aliases may be used to point to other locations.
        118 #
        119 DocumentRoot "/home/wwwroot"
        120
        121 #
        122 # Relax access to content within /var/www.
        123 #
        124 <Directory "/var/www">
        125     AllowOverride None
        126     # Allow open access:
        127     Require all granted
        128 </Directory>
        129
        130 # Further relax access to the default document root:
        131 <Directory "/home/wwwroot">
        132     #
        133     # Possible values for the Options directive are "None", "All",
        134     # or any combination of:
        135     #   Indexes Includes FollowSymLinks SymLinksifOwnerMatch ExecCGI MultiViews
        136     #
        137     # Note that "MultiViews" must be named *explicitly* --- "Options All"
        138     # doesn't give it to you.
        139     #
        140     # The Options directive is both complicated and important.  Please see
        141     # http://httpd.apache.org/docs/2.4/mod/core.html#options
        142     # for more information.
        143     #
        144     Options Indexes FollowSymLinks
        145
        146     #
        147     # AllowOverride controls what directives may be placed in .htaccess files.
        148     # It can be "All", "None", or any combination of the keywords:
        149     #   Options FileInfo AuthConfig Limit
        150     #
        151     AllowOverride None
        152
        153     #
        154     # Controls who can get stuff from this server.
        155     #
        156     Require all granted
        157 </Directory>
      ```

    * 3.重新启动`httpd`服务程序并验证效果；按理来说，只有在网站的首页面文件不存在或者用户权限不足时，才显示`httpd`服务程序的默认首页面。发现页面中显示`“Forbidden,You don't have permission to access /index.html on this server.”`。

      ```shell
        [root@localhost Desktop]# systemctl restart httpd
        [root@localhost Desktop]# firefox
      ```

      ![10-5](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587224138252.png)


## 3 `SELinux`安全子系统

* `SELinux（Security-Enhanced Linux）`是美国国家安全局在`Linux`开源社区的帮助下开发的一个强制访问控制`（MAC，Mandatory Access Control）`的安全子系统。
* `RHEL 7`系统使用`SELinux`技术的目的是为了让各个服务进程都受到约束，使其仅获取到本应获取的资源。
* `SELinux`安全子系统就是为了杜绝此类情况而设计的，它能够从多方面监控违法行为：
  * 对服务程序的功能进行限制（`SELinux`域限制可以确保服务程序做不了出格的事情）；
  * 对文件资源的访问限制（`SELinux`安全上下文确保文件资源只能被其所属的服务程序进行访问）
* `“SELinux域”`和`“SELinux安全上下文”`称为是`Linux`系统中的双保险
* `SELinux`服务有三种配置模式，具体如下:
  * `enforcing`：强制启用安全策略模式，将拦截服务的不合法请求。
  * `permissive`：遇到服务越权访问时，只发出警告而不强制拦截。
  * `disabled`：对于越权的行为不警告也不拦截。

* `SELinux`服务的配置文件`/etc/selinux/config`

  ```shell
    # This file controls the state of SELinux on the system.
    # SELINUX= can take one of these three values:
    #     enforcing - SELinux security policy is enforced.
    #     permissive - SELinux prints warnings instead of enforcing.
    #     disabled - No SELinux policy is loaded.
    SELINUX=enforcing
    # SELINUXTYPE= can take one of these two values:
    #     targeted - Targeted processes are protected,
    #     minimum - Modification of targeted policy. Only selected processes are protected.
    #     mls - Multi Level Security protection.
    SELINUXTYPE=targeted
  ```

* `getenforce`命令获得当前`SELinux`服务的运行模式

  ```shell
    [root@localhost Desktop]# getenforce
    Enforcing
  ```

* `setenforce [0|1]`命令修改`SELinux`当前的运行模式（0为禁用，1为启用）。
  * 注意，这种修改只是临时的，在系统重启后就会失效：

  ```shell
    [root@localhost Desktop]# setenforce 0
    [root@localhost Desktop]# getenforce
    Permissive
    [root@localhost Desktop]# firefox
  ```

  ![10-6](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587266859031.png)

* 前面的操作中到底是哪里出问题了呢？
  * `httpd`服务程序的功能是允许用户访问网站内容，因此`SELinux`肯定会默认放行用户对网站的请求操作。但是，我们将网站数据的默认保存目录修改为了`/home/wwwroot`，而这就产生问题了。`/home`目录是用来存放普通用户的家目录数据的，而现在，`httpd`提供的网站服务却要去获取普通用户家目录中的数据了，这显然违反了`SELinux`的监管原则。

* `SELinux`安全上下文
  * 把`SELinux`服务恢复到强制启用安全策略模式，然后分别查看原始网站数据的保存目录与当前网站数据的保存目录是否拥有不同的`SELinux`安全上下文值：

    ```shell
      [root@localhost Desktop]# setenforce 1
      [root@localhost Desktop]# ls -Zd /var/www/html
      drwxr-xr-x. root root system_u:object_r:httpd_sys_content_t:s0 /var/www/html
      [root@localhost Desktop]# ls -Zd /home/wwwroot
      drwxr-xr-x. root root unconfined_u:object_r:home_root_t:s0 /home/wwwroot
    ```

  * 在文件上设置的`SELinux`安全上下文是由用户段、角色段以及类型段等多个信息项共同组成的。其中，用户段`system_u`代表系统进程的身份，角色段`object_r`代表文件目录的角色，类型段`httpd_sys_content_t`代表网站服务的系统文件。
  
  * `semanage`命令
    * 用于管理`SELinux`的策略
    * 不仅能够像传统`chcon`命令那样—设置文件、目录的策略，还可以管理网络端口、消息接口
    * 格式为“`semanage` [选项][文件]”
    * 参数：
      * `-l`：用于查询；
      * `-a`：用于添加；
      * `-m`：用于修改；
      * `-d`：用于删除；
    * 范例：可以向新的网站数据目录中新添加一条`SELinux`安全上下文，让这个目录以及里面的所有文件能够被`httpd`服务程序所访问到

      ```shell
        [root@localhost Desktop]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot
        [root@localhost Desktop]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/*
      ```

  * `restorecon`命令将设置好的`SELinux`安全上下文立即生效。
    * `-Rv`参数对指定的目录进行递归操作,以及显示`SELinux`安全上下文的修改过程。

    * 范例：使上述配置生效，再次刷新页面，就可以正常看到网页内容了

      ```shell
        [root@localhost Desktop]# restorecon -Rv /home/wwwroot
        restorecon reset /home/wwwroot context unconfined_u:object_r:home_root_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
        restorecon reset /home/wwwroot/index.html context unconfined_u:object_r:home_root_t:s0->unconfined_u:object_r:httpd_sys_content_t:s0
      ```

## 4.个人用户主页功能

* `httpd`服务程序提供的个人用户主页功能完全可以胜任这个工作。该功能可以让系统内所有的用户在自己的家目录中管理个人的网站，而且访问起来也非常容易。

* 部署个人用户主页功能
  * 1.在`httpd`服务程序中，默认没有开启个人用户主页功能。我们需要编辑`/etc/httpd/conf.d/userdir.conf`配置文件，然后在第17行的`UserDir disabled`参数前面加上井号（#），表示让httpd服务程序开启个人用户主页功能；同时再把第24行的UserDir public_html参数前面的井号（#）去掉（UserDir参数表示网站数据在用户家目录中的保存目录名称，即public_html目录）。

    ```shell
      [root@localhost Desktop]# vim /etc/httpd/conf.d/userdir.conf
      # UserDir: The name of the directory that is appended onto a user's home
      # directory if a ~user request is received.
      #
      # The path to the end user account 'public_html' directory must be
      # accessible to the webserver userid.  This usually means that ~userid
      # must have permissions of 711, ~userid/public_html must have permissions
      # of 755, and documents contained therein must be world-readable.
      # Otherwise, the client will only receive a "403 Forbidden" message.
      #
      <IfModule mod_userdir.c>
          #
          # UserDir is disabled by default since it can confirm the presence
          # of a username on the system (depending on home directory
          # permissions).
          #
          #UserDir disabled

          #
          # To enable requests to /~user/ to serve the user's public_html
          # directory, remove the "UserDir disabled" line above, and uncomment
          # the following line instead:
          # 
          UserDir public_html
      </IfModule>

      #
      # Control access to UserDir directories.  The following is an example
      # for a site where these directories are restricted to read-only.
      #
      <Directory "/home/*/public_html">
          AllowOverride FileInfo AuthConfig Limit Indexes
          Options MultiViews Indexes SymLinksIfOwnerMatch IncludesNoExec
          Require method GET POST OPTIONS
      </Directory>
      [root@localhost Desktop]# systemctl restart httpd
    ```

  * 2.在用户家目录中建立用于保存网站数据的目录及首页面文件。另外，还需要把家目录的权限修改为755，保证其他人也有权限读取里面的内容。

    ```shell
      [root@localhost Desktop]# su - herhan
      Last login: Sat Apr 11 18:26:37 CST 2020 on :0
      [herhan@localhost ~]$ mkdir public_html
      [herhan@localhost ~]$ echo "This is herhan's website" > public_html/index.html
      [herhan@localhost ~]$ chmod -R 755 /home/herhan
    ```

  * 3.重新启动`httpd`服务程序，在浏览器的地址栏中输入网址，其格式为“网址/~用户名”（其中的波浪号是必需的，而且网址、波浪号、用户名之间没有空格），从理论上来讲就可以看到用户的个人网站了。不出所料的是，系统显示报错页面，这一定还是`SELinux`惹的祸。

    ![10-7](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587282691488.png)

  * 4.思考这次报错的原因是什么。`httpd`服务程序在提供个人用户主页功能时，该用户的网站数据目录本身就应该是存放到与这位用户对应的家目录中的，所以应该不需要修改家目录的`SELinux`安全上下文。但是，前文还讲到了`SELinux`域的概念。`SELinux`域确保服务程序不能执行违规的操作，只能本本分分地为用户提供服务。`httpd`服务中突然开启的这项个人用户主页功能到底有没有被`SELinux`域默认允许呢？

    * `getsebool`命令查询并过滤出所有与`HTTP`协议相关的安全策略。
      * 其中，`off`为禁止状态，`on`为允许状态。
      * 开启`httpd`服务的个人用户主页功能，用到的`SELinux`域安全策略应该是`httpd_enable_homedirs`吧

      ```shell
        [root@localhost Desktop]# getsebool -a | grep http
        httpd_anon_write --> off
        httpd_builtin_scripting --> on
        httpd_can_check_spam --> off
        httpd_can_connect_ftp --> off
        httpd_can_connect_ldap --> off
        httpd_can_connect_mythtv --> off
        httpd_can_connect_zabbix --> off
        httpd_can_network_connect --> off
        httpd_can_network_connect_cobbler --> off
        httpd_can_network_connect_db --> off
        httpd_can_network_memcache --> off
        httpd_can_network_relay --> off
        httpd_can_sendmail --> off
        httpd_dbus_avahi --> off
        httpd_dbus_sssd --> off
        httpd_dontaudit_search_dirs --> off
        httpd_enable_cgi --> on
        httpd_enable_ftp_server --> off
        httpd_enable_homedirs --> off
        httpd_execmem --> off
        httpd_graceful_shutdown --> on
        httpd_manage_ipa --> off
        httpd_mod_auth_ntlm_winbind --> off
        httpd_mod_auth_pam --> off
        httpd_read_user_content --> off
        httpd_run_stickshift --> off
        httpd_serve_cobbler_files --> off
        httpd_setrlimit --> off
        httpd_ssi_exec --> off
        httpd_sys_script_anon_write --> off
        httpd_tmp_exec --> off
        httpd_tty_comm --> off
        httpd_unified --> off
        httpd_use_cifs --> off
        httpd_use_fusefs --> off
        httpd_use_gpg --> off
        httpd_use_nfs --> off
        httpd_use_openstack --> off
        httpd_use_sasl --> off
        httpd_verify_dns --> off
        named_tcp_bind_http_port --> off
        prosody_bind_http_port --> off
      ```

    * `setsebool`命令来修改`SELinux`策略中各条规则的布尔值,
      * 参数：`-P` 让修改后的`SELinux`策略规则永久生效且立即生效。

      ```shell
        [root@localhost Desktop]# setsebool -P httpd_enable_homedirs=on
      ```

      ![10-8](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587284195860.png)

* 网站的拥有者并不希望直接将网页内容显示出来，只想让通过身份验证的用户访客看到里面的内容.
* 网站中添加口令功能
  * 1.使用htpasswd命令生成密码数据库。
    * `-c`参数表示第一次生成；后面再分别添加密码数据库的存放文件，以及验证要用到的用户名称（该用户不必是系统中已有的本地账户）。

    ```shell
      [root@localhost public_html]# htpasswd -c /etc/httpd/passwd herhan
      New password: 此处输入用于网页验证的密码
      Re-type new password: 再输入一遍进行确认
      Adding password for user herhan
    ```

  * 2.编辑个人用户主页功能的配置文件。把第31～35行的参数信息修改成下列内容，随后保存并退出配置文件，重启`httpd`服务程序即可生效。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf.d/userdir.conf
      27 #
      28 # Control access to UserDir directories.  The following is an example
      29 # for a site where these directories are restricted to read-only.
      30 #
      31 <Directory "/home/*/public_html">
      32     AllowOverride all
      #刚刚生成出来的密码验证文件保存路径
      33     authuserfile "/etc/httpd/passwd"
      #当用户尝试访问个人用户网站时的提示信息
      34     authname "My privately website"
      35     authtype basic
      #用户进行账户密码登录时需要验证的用户名称
      36     Require user herhan
      37 </Directory>
      [root@localhost public_html]# systemctl restart httpd
    ```
  
  * 3.当用户再想访问某个用户的个人网站时，就必须要输入账户和密码才能正常访问了。验证时使用的账户和密码是用`htpasswd`命令生成的专门用于网站登录的口令密码，而不是系统中的用户密码。

    ![10-9](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587285710604.png)

## 5.虚拟网站主机功能

* 虚拟主机功能，可以把一台处于运行状态的物理服务器分割成多个“虚拟的服务器”。但是，该技术无法实现目前云主机技术的硬件资源隔离，让这些虚拟的服务器共同使用物理服务器的硬件资源，供应商只能限制硬盘的使用空间大小。
* `Apache`的虚拟主机功能是服务器基于用户请求的不同IP地址、主机域名或端口号，实现提供多个网站同时为外部提供访问服务的技术，用户请求的资源不同，最终获取到的网页内容也各不相同。

  ![10-10](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587287513383.png)

### 5.1 基于ip地址

* 部署基于ip地址的虚拟主机
  * 1.分别在`/home/wwwroot`中创建用于保存不同网站数据的3个目录，并向其中分别写入网站的首页文件。每个首页文件中应有明确区分不同网站内容的信息，方便我们稍后能更直观地检查效果。

    ```shell
      [root@localhost public_html]# mkdir /home/wwwroot/10
      [root@localhost public_html]# mkdir /home/wwwroot/20
      [root@localhost public_html]# mkdir /home/wwwroot/30
      [root@localhost public_html]# echo "IP:192.168.10.10" > /home/wwwroot/10/index.html
      [root@localhost public_html]# echo "IP:192.168.10.20" > /home/wwwroot/20/index.html
      [root@localhost public_html]# echo "IP:192.168.10.30" > /home/wwwroot/30/index.html
    ```
  
  * 2.在`httpd`服务的配置文件中大约113行处开始，分别追加写入三个基于`IP`地址的虚拟主机网站参数，然后保存并退出。记得需要重启`httpd`服务，这些配置才生效。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      113 #
      114 <VirtualHost 192.168.10.10>
      115 DocumentRoot "/home/wwwroot/10"
      116 ServerName www.herhan.com
      117 <Directory /home/wwwroot/10>
      118 AllowOverride None
      119 Require all granted
      120 </Directory>
      121 </VirtualHost>
      122
      123 <VirtualHost 192.168.10.20>
      124 DocumentRoot "/home/wwwroot/20"
      125 ServerName bbs.herhan.com
      126 <Directory /home/wwwroot/20>
      127 AllowOverride None
      128 Require all granted
      129 </Directory>
      130 </VirtualHost>
      131 <VirtualHost 192.168.10.30>
      132 DocumentRoot "/home/wwwroot/30"
      133 ServerName tech.herhan.com
      134 <Directory /home/wwwroot/30>
      135 AllowOverride None
      136 Require all granted
      137 </Directory>
      138 </VirtualHost>
      [root@localhost public_html]# systemctl restart http
    ```

  * 3.此时访问网站，则会看到`httpd`服务程序的默认首页面。由于当前的`/home/wwwroot`目录及里面的网站数据目录的SELinux安全上下文与网站服务不吻合，因此`httpd`服务程序无法获取到这些网站数据目录。

    ```shell
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/10
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/10/*
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/20
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/20/*
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/30
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/30/*
      [root@localhost public_html]# restorecon -Rv /home/wwwroot
    ```

    ![10-11](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587289995365.png)

### 5.2 基于主机域名

* 当服务器无法为每个网站都分配一个独立`IP`地址的时候，可以尝试让`Apache`自动识别用户请求的域名，从而根据不同的域名请求来传输不同的内容。

* 部署基于主机域名的虚拟主机
  * 1.手工定义IP地址与域名之间对应关系的配置文件，保存并退出后会立即生效。可以通过分别ping这些域名来验证域名是否已经成功解析为IP地址.

    ```shell
      [root@localhost public_html]# vim /etc/hosts
      127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4
      ::1         localhost localhost.localdomain localhost6 localhost6.localdomain6
      192.168.10.10 www.herhan.com bbs.herhan.com tech.herhan.com
      [root@localhost public_html]# ping www.herhan.com
      PING www.herhan.com (192.168.10.10) 56(84) bytes of data.
      64 bytes from www.herhan.com (192.168.10.10): icmp_seq=1 ttl=64 time=0.037 ms
      64 bytes from www.herhan.com (192.168.10.10): icmp_seq=2 ttl=64 time=0.040 ms
      64 bytes from www.herhan.com (192.168.10.10): icmp_seq=3 ttl=64 time=0.041 ms
      64 bytes from www.herhan.com (192.168.10.10): icmp_seq=4 ttl=64 time=0.040 ms
    ```

  * 2.分别在`/home/wwwroot`中创建用于保存不同网站数据的三个目录，并向其中分别写入网站的首页文件。每个首页文件中应有明确区分不同网站内容的信息，方便我们稍后能更直观地检查效果。

    ```shell
      [root@localhost public_html]# mkdir /home/wwwroot/www
      [root@localhost public_html]# mkdir /home/wwwroot/bbs
      [root@localhost public_html]# mkdir /home/wwwroot/tech
      [root@localhost public_html]# echo "www.herhan.com" > /home/wwwroot/www/index.html
      [root@localhost public_html]# echo "bbs.herhan.com" > /home/wwwroot/bbs/index.html
      [root@localhost public_html]# echo "tech.herhan.com" > /home/wwwroot/tech/index.html
    ```
  
  * 3.在`httpd`服务的配置文件中大约113行处开始，分别追加写入三个基于主机名的虚拟主机网站参数，然后保存并退出。记得需要重启`httpd`服务，这些配置才生效。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      <VirtualHost 192.168.10.10>
      DocumentRoot "/home/wwwroot/www"
      ServerName "www.herhan.com"
      <Directory /home/wwwroot/www>
      AllowOverride None
      Require all granted
      </Directory>
      </VirtualHost>

      <VirtualHost 192.168.10.10>
      DocumentRoot "/home/wwwroot/bbs"
      ServerName "bbs.herhan.com"
      <Directory /home/wwwroot/bbs>
      AllowOverride None
      Require all granted
      </Directory>
      </VirtualHost>

      <VirtualHost 192.168.10.10>
      DocumentRoot "/home/wwwroot/tech"
      ServerName "tech.herhan.com"
      <Directory /home/wwwroot/tech>
      AllowOverride None
      Require all granted
      </Directory>
      </VirtualHost>
    ```

  * 4.因为当前的网站数据目录还是在`/home/wwwroot`目录中，因此还是必须要正确设置网站数据目录文件的`SELinux`安全上下文，使其与网站服务功能相吻合。最后记得用`restorecon`命令让新配置的`SELinux`安全上下文立即生效，这样就可以立即访问到虚拟主机网站了。

    ```shell
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/www
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/www/*
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/bbs/*
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/bbs
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/tech
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/tech/*
      [root@localhost public_html]# restorecon /home/wwwroot
    ```

    ![10-12](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587291269080.png)

### 5.3 基于端口号

* 基于端口号的虚拟主机功能可以让用户通过指定的端口号来访问服务器上的网站资源。在使用`Apache`配置虚拟网站主机功能时，基于端口号的配置方式是最复杂的。

* 部署基于主机域名的虚拟主机
  * 不但要考虑到目录上应用的`SELinux`安全上下文的限制，还需要考虑`SELinux`域对`httpd`服务程序的管控。
  * 1.分别在`/home/wwwroot`中创建用于保存不同网站数据的两个目录，并向其中分别写入网站的首页文件。每个首页文件中应有明确区分不同网站内容的信息，方便我们稍后能更直观地检查效果。

    ```shell
      [root@localhost public_html]# mkdir -p /home/wwwroot/6111
      [root@localhost public_html]# mkdir -p /home/wwwroot/6222
      [root@localhost public_html]# echo "port:6111" > /home/wwwroot/6111/index.hmtl
      [root@localhost public_html]# echo "port:6222" > /home/wwwroot/6222/index.hmtl
    ```

  * 2.在`httpd`服务配置文件的第43行和第44行分别添加用于监听6111和6222端口的参数。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      33 #
      34 # Listen: Allows you to bind Apache to specific IP addresses and/or
      35 # ports, instead of the default. See also the <VirtualHost>
      36 # directive.
      37 #
      38 # Change this to Listen on specific IP addresses as shown below to 
      39 # prevent Apache from glomming onto all bound IP addresses.
      40 #
      41 #Listen 12.34.56.78:80
      42 Listen 80
      43 Listen 6111
      44 Listen 6222

    ```

  * 3.在`httpd`服务的配置文件中大约113行处开始，分别追加写入两个基于端口号的虚拟主机网站参数，然后保存并退出。记得需要重启`httpd`服务，这些配置才生效。
  
    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      114 #
      115 <VirtualHost 192.168.10.10:6111>
      116 DocumentRoot "/home/wwwroot/6111"
      117 ServerName www.herhan.com
      118 <Directory /home/wwwroot/6111>
      119 AllowOverride None
      120 Require all granted
      121 </Directory>
      122 </VirtualHost>
      123 
      124 <VirtualHost 192.168.10.10:6222>
      125 DocumentRoot "/home/wwwroot/6222"
      126 ServerName bbs.herhan.com
      127 <Directory /home/wwwroot/6222>
      128 AllowOverride None
      129 Require all granted
      130 </Directory>
      131 </VirtualHost>
      [root@localhost public_html]# systemctl restart httpd.service
      Job for httpd.service failed. See 'systemctl status httpd.service' and 'journalctl -xn' for details.
      [root@localhost public_html]# systemctl status httpd.service 
      httpd.service - The Apache HTTP Server
        Loaded: loaded (/usr/lib/systemd/system/httpd.service; enabled)
        Active: failed (Result: exit-code) since Sun 2020-04-19 18:26:16 CST; 12s ago
        Process: 17719 ExecStop=/bin/kill -WINCH ${MAINPID} (code=exited, status=0/SUCCESS)
        Process: 17717 ExecStart=/usr/sbin/httpd $OPTIONS -DFOREGROUND (code=exited, status=1/FAILURE)
      Main PID: 17717 (code=exited, status=1/FAILURE)
        Status: "Total requests: 8; Current requests/sec: 0; Current traffic:   0 B/sec"

      Apr 19 18:26:16 localhost.localdomain systemd[1]: Starting The Apache HTTP Server...
      Apr 19 18:26:16 localhost.localdomain httpd[17717]: AH00558: httpd: Could not reliably determine the server's fully qualified domain name, using localhost.localdomain. Set the...his message
      Apr 19 18:26:16 localhost.localdomain httpd[17717]: (13)Permission denied: AH00072: make_sock: could not bind to address [::]:6111
      Apr 19 18:26:16 localhost.localdomain httpd[17717]: (13)Permission denied: AH00072: make_sock: could not bind to address 0.0.0.0:6111
      Apr 19 18:26:16 localhost.localdomain httpd[17717]: no listening sockets available, shutting down
      Apr 19 18:26:16 localhost.localdomain httpd[17717]: AH00015: Unable to open logs
      Apr 19 18:26:16 localhost.localdomain systemd[1]: httpd.service: main process exited, code=exited, status=1/FAILURE
      Apr 19 18:26:16 localhost.localdomain systemd[1]: Failed to start The Apache HTTP Server.
      Apr 19 18:26:16 localhost.localdomain systemd[1]: Unit httpd.service entered failed state.
      Hint: Some lines were ellipsized, use -l to show in full.

    ```

  * 4.重启`httpd`服务后，竟然出现报错信息。这是因为`SELinux`服务检测到6111和6222端口原本不属于`Apache`服务应该需要的资源，但现在却以`httpd`服务程序的名义监听使用了，所以`SELinux`会拒绝使用`Apache`服务使用这两个端口。

    ```shell
      [root@localhost public_html]# semanage port -l|grep http
      http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
      http_cache_port_t              udp      3130
      http_port_t                    tcp      80, 81, 443, 488, 8008, 8009, 8443, 9000
      pegasus_http_port_t            tcp      5988
      pegasus_https_port_t           tcp      5989
    ```

  * 5.`SELinux`允许的与`HTTP`协议相关的端口号中默认没有包含6111和6222，因此需要将这两个端口号手动添加进去。该操作会立即生效，而且在系统重启过后依然有效。

    ```shell
      [root@localhost public_html]# semanage port -a -t http_port_t -p tcp 6111
      [root@localhost public_html]# semanage port -a -t http_port_t -p tcp 6222
      [root@localhost public_html]# semanage port -l|grep http
      http_cache_port_t              tcp      8080, 8118, 8123, 10001-10010
      http_cache_port_t              udp      3130
      http_port_t                    tcp      6222, 6111, 80, 81, 443, 488, 8008, 8009, 8443, 9000
      pegasus_http_port_t            tcp      5988
      pegasus_https_port_t           tcp      5989
      [root@localhost public_html]# systemctl restart httpd.service 
    ```

  * 6.因为我们把网站数据目录存放在`/home/wwwroot`目录中，因此还是必须要正确设置网站数据目录文件的`SELinux`安全上下文，使其与网站服务功能相吻合。最后记得用`restorecon`命令让新配置的`SELinux`安全上下文立即生效。

    ```shell
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/6111
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/6222
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/6111/*
      [root@localhost public_html]# semanage fcontext -a -t httpd_sys_content_t /home/wwwroot/6222/*
      [root@localhost public_html]# restorecon /home/wwwroot
    ```

    ![10-13](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587293665888.png)

## 6.`Apache`的访问控制

* `Apache`可以基于源主机名、源IP地址或源主机上的浏览器特征等信息对网站上的资源进行访问控制。
* 通过`Allow`指令允许某个主机访问服务器上的网站资源，
* 通过`Deny`指令实现禁止访问。
* 在允许或禁止访问网站资源时，还会用到`Order`指令，这个指令用来定义`Allow`或`Deny`指令起作用的顺序，其匹配原则是按照顺序进行匹配，若匹配成功则执行后面的默认指令。
* “`Order Allow, Deny`”表示先将源主机与允许规则进行匹配，若匹配成功则允许访问请求，反之则拒绝访问请求。

* 范例：`Apache`的访问控制演示
  * 1.先在服务器上的网站数据目录中新建一个子目录，并在这个子目录中创建一个包含`Successful`单词的首页文件。

    ```shell
      [root@localhost public_html]# mkdir /var/www/html/server
      [root@localhost public_html]# echo "Successful" > /var/www/html/server/index.html
    ```

  * 2.打开`httpd`服务的配置文件，在第129行后面添加下述规则来限制源主机的访问。这段规则的含义是允许使用`Firefox`浏览器的主机访问服务器上的首页文件，除此之外的所有请求都将被拒绝。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      #
      <Directory /var/www/html/server>
      SetEnvIf User-Agent "Firefox" ff=1
      Order allow,deny
      Allow from env=ff
      </Directory>
      #
      [root@localhost public_html]# systemctl restart httpd.service
    ```

    ![10-14](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587294811111.png)

  * 3.除了匹配源主机的浏览器特征之外，还可以通过匹配源主机的`IP`地址进行访问控制。例如，我们只允许`IP`地址为192.168.10.20的主机访问网站资源，那么就可以在`httpd`服务配置文件的第129行后面添加下述规则。

    ```shell
      [root@localhost public_html]# vim /etc/httpd/conf/httpd.conf
      #
      <Directory /var/www/html/server>
      Order allow,deny
      Allow from 192.168.10.20
      </Directory>
      #
      [root@localhost public_html]# systemctl restart httpd.service
    ```

    ![10-15](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/UTOOLS1587295288492.png)