<!--
 * @Author: your name
 * @Date: 2020-12-13 15:38:45
 * @LastEditTime: 2020-12-18 23:56:40
 * @LastEditors: Please set LastEditors
 * @Description: In User Settings Edit
 * @FilePath: \github\notebook\Linux\Linux-高性能Web集群实践-day2.md
-->

Linux-高性能Web集群实践
===
<!-- TOC -->

- [Linux-高性能Web集群实践](#linux-高性能web集群实践)
- [Day02.中小规模Web集群规划与环境准备](#day02中小规模web集群规划与环境准备)
  - [1.集群概念](#1集群概念)
  - [2.企业网站集群架](#2企业网站集群架)
  - [3.网站集群架构搭建规划](#3网站集群架构搭建规划)
    - [3.1 中小规模基础网站集群架构规划](#31-中小规模基础网站集群架构规划)
    - [3.2网站集群搭建顺序规划](#32网站集群搭建顺序规划)
    - [3.3主机IP及主机名规划表](#33主机ip及主机名规划表)
    - [3.4安装及配置路径等规划](#34安装及配置路径等规划)
  - [4.网站集群架构系统优化](#4网站集群架构系统优化)
    - [4.1 集群节点模板机统一基础优化](#41-集群节点模板机统一基础优化)
    - [4.2 集群节点模板机统一扩展优化](#42-集群节点模板机统一扩展优化)
    - [4.3 集群节点模板机统一优化总结](#43-集群节点模板机统一优化总结)

<!-- /TOC -->
# Day02.中小规模Web集群规划与环境准备

## 1.集群概念

* 集群:是指一组(若干个)互相独立的计算机,利用高速通讯网络组成的一个较大的计算机服务系统,每个集群节点(集群中的每台计算机)都是运行各自服务的独立服务器;这些服务器之间可以彼此通信,协同向用户提供应用程序、系统资源和数据,并以单一系统的模式加以管理;当用户客户机请求集群系统时,集群给用户的感觉就是一个单一独立的服务器,而实际上用户请求的是一组集群服务器。

* 集群的特点
  * (1)高并发,大流量
  * (2)持续服务,高可用
  * (3)海量数据
  * (4)用户分布广泛,网络情况复杂

## 2.企业网站集群架

* 网站用户环节
* 网站安全环节
* 网站通信环节
* 网站反向代理负载均衡环节
* 网站Web服务环节
* 网站数据库存储环节
* 网站缓存环节
* 网站数据备份环节
* 网站批量管理环节
* 网站监控管理环节

<table>
    <tr>
        <td>架构组成</td> 
        <td>作用说明</td> 
   </tr>
    <tr>
        <td>网站访问用户</td> 
        <td>请求访问网站信息资源的人员,如果一个网站集群没有用户进行访问,那搭建网站集群也是没有意义的</td> 
   </tr>
   <tr>
        <td>防火墙设备</td>
        <td>阻挡非正常访问请求,保障网站集群架构安全性,防止恶意攻击者影响网络稳定运行,开源工具有Iptables</td>
   </tr>
   <tr>
        <td colspan="2"><center>外网交换机设备(访问区域 = 架构前端)</center></td>
   </tr>
    <tr>
        <td>负载均衡服务器</td>
        <td>负责将访问请求调度到不同表的Web服务器上,主要起调度作用,常见的有Nginx、Lvs、Haproxy</td>
    </tr>
    <tr>
        <td>Web服务器</td>
        <td>接收用户访问请求,并对请求进行处理,将请求信息抛送给后端服务器,最终将处理结果进行回复响应</td>
    </tr>
    <tr>
        <td colspan="2"><center>内网交换价设备(安全区域 = 架构后端)</center></td>
    </tr>
    <tr>
        <td>数据库服务器</td>
        <td>用于存储与用户访问相关的数据信息,即存放数据的仓库,一般存储的主要是文本内容等。主要的应用软件有MySQL、SOL Server、Oracle</td>
    </tr>
    <tr>
        <td>存储服务器</td>
        <td>用于储存于用户访问相关的数据信息，一般储存一些音频、视频、图片、附件等信息。主要的应用软件有NFS、MooseFS(MFS)\GlusterFS\FastDFS等</td>
    </tr>
    <tr>
        <td>内存数据库服务器</td>
        <td>利用硬件内存存储用户数据信息，提高数据存储与读取效率，作为数据库的补充，主要应用的软件有Memcached、Redis</td>
    </tr>
    <tr>
        <td>备份服务器</td>
        <td>主要作为全网服务器的数据备份服务器，主要储存备份配置文件、脚本文件、代码文件、日志文件、数据库备份文件等。同时也作为存储服务器的热备服务器，当存储服务器出现异常时，可以实现快速切换存储服务，避免访问获取数据异常的情况</td>
    </tr>
    <tr>
        <td colspan="2"><center>架构扩展功能服务器</center></td>
    </tr>
    <tr>
        <td>批量管理服务器</td>
        <td>负责全网架构服务器的管理工作，涉及批量修改服务器配置文件、批量查看服务器信息、批量部署服务，以及批量分发文件等。主要应用的软件有SSH（分发密码钥）、Ansible</td>
    </tr>
    <tr>
        <td>监控服务器</td>
        <td>负责监控全网服务器的系统性能情况、业务处理情况、服务运行情况等，一旦架构服务器出现异常会发送告警信息给运维人员。主要应用的软件有Zabbix、Nagios、Cacti等</td>
    </tr>
    <tr>
        <td>审计服务器</td>
        <td>负责登录架构前的用户身份验证，只有符合要求的管理人员才能通过此服务器管理其他服务器设备，也称为跳板及服务器。由于服务器权限职能过高，因此绝对不能提供外网直连访问权限，既不能配置外网地址</td>
    </tr>
    <tr>
        <td>VPN服务器</td>
        <td>用于外网用户进行远程VPN连接，实现远程安全登录网络架构远程管理架构服务器</td>
    </tr>
</table>

## 3.网站集群架构搭建规划

### 3.1 中小规模基础网站集群架构规划

|服务器规划|数量|名称规划|
|---------|----|-------|
|Nginx负载均衡服务器|两台|对访问网站的流量进行分流，减少流量对某台服务器造成的压力|
|动态Web服务器|两台|处理用户动态页面访问请求（Nignx）|
|静态Web服务器|两台|为用户提供上传的图片、附件、视频（放于存储通中）等浏览服务|
|NFS存储服务器|一台|存储图片、附件、头像、等静态数据|
|Rsync备份服务器|一台|对全网服务器数据进行定时备份、NFS存储实时备份|
|MySQL数据库服务器|一台|对用户发布的文字文本数据进行存储|
|管理服务器|一台|主要是集群内部的管理相关功能业务<br>1.作为yum仓库服务器，提供全网服务器的软件下载<br>2.跳板机、操作审计、VPN(OpenVPN)、监控(Zabbix)<br>3.批量分发和管理（SSH key+Ansible）<br>4.无人值守系统安装服务kickstart（cobbler）<br>如果机器够用，可以将应用分拆为不同的服务器独立实现服务|

### 3.2网站集群搭建顺序规划

|部署里程|部署服务|部署说明|
|-------|-------|-------|
|第一里程|网站备份服务-Rsync|完成数据存储备份服务部署，网站永远数据最重要|
|第二里程|网站存储服务-NFS|实现网站数据的统一存储，节省服务器磁盘硬件成本|
|第三里程|实时复制服务（部署在NFS上）|（1）利用Inotify+Rsync实现新增数据实时同步备份<br>（2）利用Sersync+Rsync实现新增数据实时同步备份|
|第四里程|批量管理服务-SSH、Ansible|（1）熟悉掌握远程管理服务是部署批量管理服务的前提<br>（2）部署批量管理服务，并掌握批量管理方法|
|第五里程|网络架构部署-LNMP|（1）掌握网站HTTP协议原理概念知识<br>（2）部署网站静态资源处理服务-Nginx<br>（3）部署网站动态资源处理服务-PHP<br>（4）部署网站数据库服务器-MySQL|
|第六里程|网站架构部署-Nignx静态|（1）部署网站静态资源处理服务-Nginx（选做）|
|第七里程|负载均衡服务-Nignx|（1）完成网站负载压力平均分配，提升网站高并发处理能力|
|第八里程|网站冗余服务-Keepalived|（1）处理网站可能出现的单点故障问题|

### 3.3主机IP及主机名规划表

|服务器说明|eth0外网IP(NAT)|eth1内网IP(NAT)|主机名称规划|
|----|----|----|----|
|A1-负载服务器01|10.0.0.5/24|172.16.1.5/16|lb01|
|A2-负载服务器02|10.0.0.6/24|172.16.1.6/16|lb02|
|B1-Web服务器01|10.0.0.7/24|172.16.1.7/16|web01|
|B2-Web服务器02|10.0.0.8/24|172.16.1.8/16|web02|
|SB1-Web服务器01|10.0.0.9/24|172.16.1.9/16|sweb01|
|SB2-Web服务器02|10.0.0.10/24|172.16.1.10/16|sweb02|
|C1-NFS存储服务器|10.0.0.31/24|172.16.1.31/16|nfs01|
|C2-Rsync备份服务器|10.0.0.41/24|172.16.1.41/16|backup|
|C3-MySQL数据库服务器|10.0.0.51/24|172.16.1.51/16|db01|
|X-管理服务器|10.0.0.61/24|172.16.1.61/16|m01|

### 3.4安装及配置路径等规划

|目录说明|目录结构说明|
|----|----|
|`/server/scripts`|服务器本地存放脚本程序的目录|
|`/server/tools`|服务器本地存放软件安装包的目录|
|`/application/软件名`|服务器本地软件安装的根目录，软件名不带版本号。例如：`/application/nginx,/application/mysql`|
|`/application/nginx/html`|Web服务器站点目录(bbs,blog,www,edu)|
|`/application/nginx/logs`|Web服务器日志{bbs|log|www}_access.log|
|`/backup`|服务器本地数据备份目录，也是备份服务器Rsync的备份目录|
|`/data`|NFS共享存储共享文件目录|

## 4.网站集群架构系统优化

### 4.1 集群节点模板机统一基础优化

* 1.基础优化操作项：规范功能目录
  * 创建存储软件目录（`/server/tools`）
  * 创建存储脚本目录（`/server/scripts`）

    ```shell
        # mkdir -p /server/tools
        # mkdir -p /server/scripts
    ```

* 2.基础优化操作项：配置hosts解析信息
  * 设置本地服务主机名称与IP地址映射关系
    
    ```shell
        # 配置/etc/hosts解析文件记录信息
        [root@www ~]# cat >/etc/hosts<<EOF
                    127.0.0.1 localhost localhost.localdomain localhost4 localhost4.localdomain4
                    ::1       localhost localhost.localdomain localhost6 localhost6.localdomain6
                    172.16.1.5 lb01
                    172.16.1.6 lb02
                    172.16.1.7 web01
                    172.16.1.8 web02
                    172.16.1.9 sweb01
                    172.16.1.10 sweb02
                    172.16.1.31 nfs01
                    172.16.1.41 backup
                    172.16.1.51 db01 db01.etiantian.org
                    172.16.1.61 m01
                    EOF
    ```

* 3.基础优化操作项：修改主机名称

    ```shell
        [root@www ~]# hostnamectl set-hostname backup   #<==设置主机名为backup
    ```

* 4.基础优化操作项：更新yum源信息
  * 第一步，使用yum源地址，安装软件更快。

    ```shell
        [root@www ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
        [root@www ~]# curl -o /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
        [root@www ~]# yum makecache
    ```

  * 第二步，安装RHEL/CentOS官方源不提供的软件包
  
    ```shell
        [root@www ~]# mv /etc/yum.repos.d/epel.repo /etc/yum.repos.d/epel.repo.backup
        [root@www ~]# mv /etc/yum.repos.d/epel-testing.repo /etc/yum.repos.d/epel-testing.repo.backup
        [root@www ~]# wget -O /etc/yum.repos.d/epel.repo http://mirrors.aliyun.com/repo/epel-7.repo
        Centos-7.repo
        [root@www ~]# yum makecache
    ```

* 5.基础优化操作项：优化安全设置
  * 对于内网环境而言，部分安全方面的设置是非必需的，可以进行关闭，以避免不必要的安全策略影响系统服务的正常运行。
  * 1.关闭SELinux
   
    ```shell
        [root@www ~]# sed -i 's#SELINUX=disabled#g' /etc/selinux/config
        [root@www ~]# sed -i 's/SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
        [root@www ~]# grep SELINUX=disabled /etc/selinux/config   #<==查看修改SELinux的状态
        [root@www ~]# setenforce 0      #<==临时关闭SELinux
        [root@www ~]# getenforce        #<==查看SELinux状态
    ```
    
  * 2.关闭firewalld防火墙服务
  
    ```shell
        [root@www ~]# systemctl disable firewalld   #<==禁止firewalld防火墙开机自启动
        [root@www ~]# systemctl stop firewalld      #<==关闭firewalld防火墙服务
        [root@www ~]# systemctl status firewalld    #<==查看firewalld关闭的状态
        #说明：通知系统的防火墙功能，切记只是针对局域网内部的机器关闭，连接外网的服务器设备还需开启
    ```

* 6.基础优化操作项：精简开机启动程序
  * 企业生产最小化原则有以下几个：
    * 1）安装软件包最小化
    * 2）用户权限最小化
    * 3）目录文件权限最小化
    * 4）自启动服务最小化
    * 5）服务运行用户最小化
    
    ```shell
       [root@www ~]# systemctl list-unit-files | grep enable | egrep -v "sshd.service|crond.service|sysstat|rsyslog|^NetworkManager.service|irqbalance.service"|awk '{print "systemctl disable",$1}'|bash     #<==批量处理命令
       [root@www ~]# systemctl list-unit-files | grep enable     #<==处理后结果
       autovt@.service                               enabled 
       crond.service                                 enabled 
       getty@.service                                enabled 
       irqbalance.service                            enabled 
       rsyslog.service                               enabled 
       sshd.service                                  enabled 
       sysstat.service                               enabled
       [root@www ~]# netstat -lntup     #<==重启后查看系统开启的端口情况
       Active Internet connections (only servers)
       Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
       tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      27170/sshd                  
       tcp6       0      0 :::22                   :::*                    LISTEN      27170/sshd    
    ```

* 7.基础优化操作项：设置普通用户提权操作（可选优化）
  * 对于系统运维工作中，一些普通用户需要分担超级管理员root的工作压力，因此需要将相应的root权限进行下放，所以要将相应普通用户进行提权。
  ```shell
    # 提权herhan可以利用sudo
    [root@www ~]# useradd herhan
    [root@www ~]# echo herhan|passwd --stdin herhan
    [root@www ~]# cp /etc/sudoers /etc/sudoers.ori
    [root@www ~]# echo "herhan ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
    [root@www ~]# tail -l /etc/sudoers
    [root@www ~]# visudo -c
  ```

* 8.基础优化操作项：设置系统字符集
  * 设置系统字符集的命令如下。
  ```shell
    #设置系统中文UTF-8字符集
    [root@www ~]# cat /etc/locale.conf
    LANG="en_US.UTF-8"
    [root@www ~]# cp /etc/locale.conf{,.ori}
    [root@www ~]# localectl set-locale LANG="zh_CN.UTF-8"
    [root@www ~]# cat/etc/locale.confg
    LANG=zh_CN.UTF-8
  ```

* 9.基础优化操作项：时间同步设置
  ```shell
    #设置系统时间同步（Chrony是CentOS7推荐的时间服务器）
    [root@www ~]# yum install ntpdate -y  #<==这里依然使用NTP时间服务软件。
    [root@www ~]# /usr/sbin/ntpdate ntp3.aliyun.com
    [root@www ~]# echo '#crond-id-001:time sync by herhan' >> /var/spool/cron/root
    [root@www ~]# echo "*/5 * * * * /usr/sbin/ntpdate ntp3.aliyun.com > /dev/null 2>&1" >>/var/spool/cron/root
    [root@www ~]# crontab -l
  ```

* 10.基础优化操作项：提升命令行操作安全性（可选优化，测试环境下建议不做）
  ```shell
    #提升命令行操作安全性的命令如下
    [root@www ~]# echo 'export TMOUT=300' >>/etc/profile    #<==设置会话超时时间为300秒
    [root@www ~]# echo 'export HISTSIZE=5' >>/etc/profile    #<==命令行历史记录保留5个
    [root@www ~]# echo 'export HISTFILESZIE=5' >>/etc/profile   #<==历史记录文件保留5个
    [root@www ~]# tail -3 /etc/profile
    [root@www ~]# . /etc/profile
  ```

* 11.基础优化操作项：加大文件描述符
  * 一般情况下，一台服务器被访问时会启用很多的程序，而启动每一个程序的进程都会占用文件服务器描述符，因此有时需要加大文件描述符的大小，从而不致影响到程序的启动。
  ```shell
    [root@backup ~]# echo '*      -      noflie      65535' >>/etc/security/limits.conf  #说明：永久生效，但需要重新进入系统查看配置文件是否生效 
    [root@backup ~]# tail -l /etc/security/limits.conf
    [root@backup ~]# ulimit -SHn 65535  #说明：加大文件描述符命令，但只是临时的，重启系统或重新登录系统后需要重新设置
    [root@backup ~]# ulimit -n    #<==命令方式查看配置结果
  ```

* 12.基础优化操作项：优化系统内核
  * 利用相应内核参数数值的优化，从而影响系统的安全性、稳定性、高效性
  ```shell
    [root@backup ~]# cat >>/etc/sysctl.conf<<EOF
    > net.ipv4.tcp_fin_timeout = 2
    > net.ipv4.tcp_wn_reuse = 1
    > net.ipv4.tcp_wn_recycle = 1
    > net.ipv4.tcp_syncookies  =1
    > net.ipv4.tcp_keepalive_time = 600
    > net.ipv4.ip_local_port_range = 4000    65000
    > net.ipv4.tcp_max_syn_backlog = 16384
    > net.ipv4.tcp_max_tw_buckets = 36000
    > net.ipv4.route.gc_without = 100
    > net.ipv4.tcp_syn_retries = 1
    > net.core.somaxconn = 16384
    > net.core.netdev_max_backlog = 16384
    > net.ipv4.tcp_max_orphans = 16384
    > #以下参数是对Iptables防火墙的优化，防火墙不开会提示，可以忽略。
    > net.nf_conntrack_max = 25000000
    > net.netfilter.nf_conntrack_max = 25000000
    > net.netfilter.nf_conntrack_tcp_timeout_established = 180
    > net.netfilter.nf_conntrack_tcp_timeout_time_wait = 120
    > net.netfilter.nf_conntrack_tcp_timeout_close_wait = 60
    > net.netfilter.nf_conntrack_tcp_timeout_fin_wait = 120
    > net.core.wmem_default = 8388608
    > net.core.rmem_default = 8388603
    > net.core.wmem_max = 16777216
    > net.core.rmem_max = 16777216
    > EOF
    [root@backup ~]# sysctl -p
  ```

* 13.基础优化操作项：安装系统常用软件
  ```shell
    [root@backup ~]# yum install tree nmap dos2unix lrzsz nc lsof wget tcpdump htop iftop iotop sysstat nethogs -y
    [root@backup ~]# yum install psmisc net-tools bash-completion vim-enhanced -y
  ```

* 14.基础优化操作项：优化SSH远程连接效率
  * 配置远程连接SSH配置文件，使之连接效率更高、连接更安全。
  ```shell
    [root@backup ~]# cp /etc/ssh/sshd_config{,.ori}  
    # 说明：备份
    [root@backup ~]# sed -i -e "17s/.*/Port 52113/g" /etc/ssh/sshd_config  
    # 说明：修改SSH服务监听的默认22端口，使用自定义指定的监听端口进行连接
    [root@backup ~]# sed -i -e "19s/.*/ListenAddress 172.16.1.41/g" /etc/ssh/sshd_config  
    # 说明：修改SSH服务监听的默认IP地址，默认为0.0.0.0全部监听，可以自定义制定监听内网IP地址
    [root@backup ~]# sed -i -e "38s/.*/PermitRootLogin no/g" /etc/ssh/sshd_config
    # 说明：关闭SSH登录root用户权限，提升系统安全性
    [root@backup ~]# sed -i -e "64s/.*/PermitEmptyPasswords no/g" /etc/ssh/sshd_config
    # 禁止空密码用户SSH连接登录系统
    # 以上均采用sed替换方式修改SSH服务配置文件，替换时指定的行号会根据不同版本的系统有变化，最好以工作中常用的系统为主，修改相应行号。
    [root@backup ~]# vim /etc/ssh/sshd_config
    ...
    PermitEmptyPasswords no
    UseDNS no
    GSSAPIAuthentication no
    ... 
  ```

* 15.基础优化操作项：锁定关键系统文件优化
  * 对关键系统账号文件和启动文件进行锁定，防止用户篡改。
  ```shell
    [root@backup ~]# chattr +i /etc/passwd /etc/shadow /etc/group       #<==锁定关键账户文件，防删防改
    [root@backup ~]# chattr +i /etc/inittab /etc/fstab /etc/sudoers      #<==锁定其他关键文件
    [root@backup ~]# lsattr /etc/passwd /etc/shadow /etc/group /etc/inittab /etc/fstab /etc/sudoers    #<==查看结果
    ----i----------- /etc/passwd
    ----i----------- /etc/shadow
    ----i----------- /etc/group
    ----i----------- /etc/inittab
    ----i----------- /etc/fstab
    ----i----------- /etc/sudoers
    [root@backup ~]# mv /usr/bin/chattr /opt/herhan     #<==移走关键命令
    [root@backup ~]# cp /usr/bin/xargs /usr/bin/chattr      #<==用其他命令伪装一下，用户需妥善保存关键命令
  ```

* 16.基础优化操作项：清理系统版本信息
  * 清理系统版本信息的命令如下。
  ```shell
    [root@backup ~]# cat /etc/issue
    \S
    Kernel \r on an \m

    [root@backup ~]# cat /etc/issue.net
    \S
    Kernel \r on an \m
    [root@backup ~]# >/etc/issue
    [root@backup ~]# >/etc/issue.net
    [root@backup ~]# cat /etc/issue
  ```

* 17.基础优化操作项：为GRUB增加密码
  ```shell
    [root@backup ~]# grub2-setpassword
    Enter password: 
    Confirm password: 
    [root@backup ~]# cat /boot/grub2/user.cfg 
    GRUB2_PASSWORD=grub.pbkdf2.sha512.10000.54404F8E6EB8A211A644D47E2D2104E056AE9AECAB661919C8B112208EB7E0EC70F9B633B698559F5BAB8E3AE5662D283AE4F9DD85E02EA2219C89F998C34F21.8D3BB5396F73F4103D26B008E40D4CF25F4BB2966C2D7941A70F03471B6D319A4C59254F01754665826EBD48B0C7B555D7A26F9FC8759B371E21F6673FF4A1CE
  ```

* 18.基础优化操作项：禁止用户ping服务器IP地址
  * 禁止用户ping服务器IP地址的目的，也是让黑客用户以为服务器停机或者不可用，或禁止返回有效信息
  ```shell
    [root@backup ~]# echo "net.ipv4.icmp_echo_ignore_all=1" >> /etc/sysctl.conf
    [root@backup ~]# sysctl -p
    net.ipv4.icmp_echo_ignore_all=1
  ```

### 4.2 集群节点模板机统一扩展优化

* 1.扩展优化操作项：优化系统提示符（可根据个人喜好选择）
  ```shell
    [root@backup ~]# echo "PS1='\[\e[32;1m\][\u@\h \W]\\$ \[\e[0m\]'" >>/etc/profile
    [root@backup ~]# source /etc/profile
  ```

* 2.扩展优化操作项：定义系统别名
  * 系统别名只是系统中的一个小功能，虽然功能简单，但用处非常大。有时需要输入的命令会跟上一长串的参数信息，而这样的命令又经常使用，因此定义别名可以简化命令的输出，提出工作的效率。
  ```shell
    [root@backup ~]# echo "alias grep='grep --color=auto'" >>/etc/profile
    # 说明：定义grep命令查找的内容有颜色显示，便于查看，CentOS7默认已经配置好了。
    [root@backup ~]# echo "alias ll='ls -l --color=auto --time-style=long-iso'" >>/etc/profile
    # 说明：定义ls命令的长格式显示，是输出信息格列完全对齐显示
    [root@backup ~]# source /etc/profile
    # 提示：以上只是笔者工作中常用的基本别名设置，读者可根据实际工作情况自行定义。
  ```

* 3.扩展优化操作项：修改yum.conf文件配置信息
  ```shell
    [root@backup ~]# vim /etc/yum.conf
    [main]
    cachedir=/var/cache/yum/$basearch/$releasever    #<==定义保存yum安装软件包的默认路径
    # 说明：$basearch变量信息默认为 # uname -m命令输出结果，即显示系统是64位还是32位平台信息
    #       $releasever变量信息默认为系统的发行版本信息，如果是CentOS6版本下载的，发行版本为6，即显示6作为目录，以上只是保存的默认路径信息，软件包下载时也有默认保存路径。
    keepcache=1         #<==设置为1表示保存yum安装软件包，设置为0表示不保存
    debuglevel=2        #<==调试级别（0-10），默认为2（具体调试级别的应用，笔者也不了解）
    logfile=/var/log/yum.log      #<==yum的日志文件所在的位置
    exactarch=1           #<==在更新的时候，是否允许更新不同版本的rpm包，比如是否在i386上更新i686的rpm包
    obsoletes=1           #<==这是一个update的参数，具体请参阅yum(8)，简单来说就是相当于upgrade，允许更新陈旧的rpm包
    gpgcheck=1            #<==是否检查GPG(GNU Private Guard)，一种秘钥方式签名。
    plugins=1             #<==是否允许使用插件，默认是0不允许，但是我们一般会用yum-fastestmirror这个插件
    installonly_limit=5   #<==允许保留多少个内核包
    bugtracker_url=http://bugs.centos.org/set_project.php?project_id=23&ref=http://bugs.centos.org/bug_report_page.php?category=yum
    distroverpkg=centos-release
  ```

* 4.扩展优化操作项：自动快速配置主机名和IP地址
  ```shell
    [root@backup scripts]# vim modify.sh
    #!/bin/sh
    if [ $# -ne 2];then
      echo "/bin/sh $0 hostname PartIP"
      exit 1
    fi
    hostnamectl set-hostname $1
    sed -i "/IPADDR/s#200#$2#g" /etc/sysconfig/network-scripts/ifcfg-eth0   #<==模板机IP最后8位默认是200
    sed -i "/IPADDR/s#200#$2#g" /etc/sysconfig/network-scripts/ifcfg-eth1
    systemctl restart network
    exit
  ```

### 4.3 集群节点模板机统一优化总结
* Linux基础优化与安全重点小结
  * 1）不用root登录管理系统，而以普通用户登录通过sudo授权管理。
  * 2）更改默认的远程连接SSH服务端口，禁止root用户远程连接，甚至更改SSH服务只监听内网IP（本章未优化端口、用户以及服务监听的IP）。
  * 3）定时自动更新服务器的时间，使其和互联网时间同步。
  * 4）配置yum更新源，从国内更新源下载安装软件包。
  * 5）关闭SELinux及Iptables/firewalld。在工作场景中，如果有外部IP一般要打开防火墙，高并发高流量服务器可能无法开启。
  * 6）调整文件描述符的大小，进程及文件的打开都会消耗文件描述符。
  * 7）精简并保留必要的开机自启动服务（如crond、sshd、Network、rsyslog、sysstat、irqbalance）.
  * 8）Linux内核参数优化/etc/sysctl.conf，执行sysctl -p生效。
  * 9）更改系统字符集为“zh_CN.UTF-8”,使其支持中文，防止出现乱码。
  * 10）锁定关键系统文件如/etc/passwd、/etc/shadow、/etc/group、/etc/gshadow、/etc/inittab,处理以上内容后把chattr、lsattr改名为herhan并转移，这样就安全多了
  * 11）清空/etc/issue、/etc/issue.net，去除系统及内核版本登录前的屏幕显示。
  * 12）清除多余的系统虚拟用户账号（不是在严苛环境下不做）
  * 13）为GRUB引导菜单加密码。
  * 14）禁止主机被ping
  * 15）打补丁升级系统及有已知漏洞的软件。
  * 16）全网域名和自生内网IP实现hosts对应解析。
  * 17）安装系统默认没有的常用工具。
  * 18）提升命令行操作安全性（超时历史记录）