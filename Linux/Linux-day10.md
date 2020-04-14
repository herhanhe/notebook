# Linux-day10 使用`Apache`服务部署静态网站

---

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

* DocumentRoot参数用于定义网站数据的保存路径，其参数的默认值是把网站数据存放到/var/www/html目录中，而当前网站普遍的首页面名称是index.html；