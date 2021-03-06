# Linux_day9 使用ssh服务管理远程主机

===

## 1.配置网卡服务
### 1.1 配置网卡参数
* `nmtui` 配置网卡
  
  ```shell
    [root@herhan ~]# nmtui
  ```
  ![9-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/9-1.png)
  ![9-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/9-2.png)
  ![9-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/9-3.png)

* `ifcfg-eth0` 配置网卡
  ```shell
    [root@linuxprobe ~]# vim /etc/sysconfig/network-scripts/ifcfg-eth0
      TYPE=Ethernet
      BOOTPROTO=none
      DEFROUTE=yes
      IPV4_FAILURE_FATAL=no
      IPV6INIT=yes
      IPV6_AUTOCONF=yes
      IPV6_DEFROUTE=yes
      IPV6_FAILURE_FATAL=no
      NAME=eno16777736
      UUID=ec77579b-2ced-481f-9c09-f562b321e268
      ONBOOT=yes
      IPADDR0=192.168.10.10
      HWADDR=00:0C:29:C4:A4:09
      PREFIX0=24
      IPV6_PEERDNS=yes
      IPV6_PEERROUTES=yes
  ```

  * 重启网卡，使配置生效
  ```shell
    [root@herhan ~]# systemctl restart network
  ```

  ### 1.2 创建网络会话
  * `NetworkManager`
    * 提供网络服务，这是一种动态管理网络配置的守护进程，能够让网络设备保持连接状态。
  * `nmcli`命令来管理`Network Manager`服务
  * `nmcli`是一款基于命令行的网络配置工具
  * 查看网络信息或网络状态
  ```shell
    [root@herhan ~]# nmcli connection show
      NAME         UUID                                  TYPE            DEVICE 
      System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  802-3-ethernet  eth0    
    [root@herhan ~]# nmcli con show "System eth0"
      connection.id:                          System eth0
      connection.uuid:                        5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
      connection.stable-id:                   --
      connection.interface-name:              eth0
      connection.type:                        802-3-ethernet
      connection.autoconnect:                 yes
      connection.autoconnect-priority:        0
      connection.timestamp:                   1615185160
      connection.read-only:                   no
      connection.permissions:                 
      connection.zone:                        external
      connection.master:                      --
      connection.slave-type:                  --
      connection.autoconnect-slaves:          -1 (default)
      connection.secondaries:                 
      connection.gateway-ping-timeout:        0
      connection.metered:                     unknown
      connection.lldp:                        -1 (default)
      802-3-ethernet.port:                    --
      802-3-ethernet.speed:                   0
      802-3-ethernet.duplex:                  --
      802-3-ethernet.auto-negotiate:          yes
      802-3-ethernet.mac-address:             --
      802-3-ethernet.cloned-mac-address:      --
      802-3-ethernet.generate-mac-address-mask:--
      802-3-ethernet.mac-address-blacklist:   
      802-3-ethernet.mtu:                     auto
      802-3-ethernet.s390-subchannels:        
      802-3-ethernet.s390-nettype:            --
      802-3-ethernet.s390-options:            
      802-3-ethernet.wake-on-lan:             1 (default)
      802-3-ethernet.wake-on-lan-password:    --
      ipv4.method:                            auto
      ipv4.dns:                               
      ipv4.dns-search:                        
      ipv4.dns-options:                       (default)
      ipv4.dns-priority:                      0
      ipv4.addresses:                         
      ipv4.gateway:                           --
      ipv4.routes:                            
      ipv4.route-metric:                      -1
      ipv4.ignore-auto-routes:                no
      ipv4.ignore-auto-dns:                   no
      ipv4.dhcp-client-id:                    --
      ipv4.dhcp-timeout:                      0
      ipv4.dhcp-send-hostname:                yes
      ipv4.dhcp-hostname:                     --
      ipv4.dhcp-fqdn:                         --
      ipv4.never-default:                     no
      ipv4.may-fail:                          yes
      ipv4.dad-timeout:                       -1 (default)
      ipv6.method:                            ignore
      ipv6.dns:                               
      ipv6.dns-search:                        
      ipv6.dns-options:                       (default)
      ipv6.dns-priority:                      0
      ipv6.addresses:                         
      ipv6.gateway:                           --
      ipv6.routes:                            
      ipv6.route-metric:                      -1
      ipv6.ignore-auto-routes:                no
      ipv6.ignore-auto-dns:                   no
      ipv6.never-default:                     no
      ipv6.may-fail:                          yes
      ipv6.ip6-privacy:                       -1 (unknown)
      ipv6.addr-gen-mode:                     stable-privacy
      ipv6.dhcp-send-hostname:                yes
      ipv6.dhcp-hostname:                     --
      ipv6.token:                             --
      GENERAL.NAME:                           System eth0
      GENERAL.UUID:                           5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03
      GENERAL.DEVICES:                        eth0
      GENERAL.STATE:                          activated
      GENERAL.DEFAULT:                        yes
      GENERAL.DEFAULT6:                       no
      GENERAL.VPN:                            no
      GENERAL.ZONE:                           external
      GENERAL.DBUS-PATH:                      /org/freedesktop/NetworkManager/ActiveConnection/0
      GENERAL.CON-PATH:                       /org/freedesktop/NetworkManager/Settings/0
      GENERAL.SPEC-OBJECT:                    /
      GENERAL.MASTER-PATH:                    --
      IP4.ADDRESS[1]:                         172.30.199.136/20
      IP4.GATEWAY:                            172.30.207.253
      IP4.ROUTE[1]:                           dst = 169.254.0.0/16, nh = 0.0.0.0, mt = 1002
      IP4.DNS[1]:                             100.100.2.136
      IP4.DNS[2]:                             100.100.2.138
      DHCP4.OPTION[1]:                        requested_routers = 1
      DHCP4.OPTION[2]:                        requested_domain_search = 1
      DHCP4.OPTION[3]:                        requested_time_offset = 1
      DHCP4.OPTION[4]:                        requested_domain_name = 1
      DHCP4.OPTION[5]:                        requested_rfc3442_classless_static_routes = 1
      DHCP4.OPTION[6]:                        requested_classless_static_routes = 1
      DHCP4.OPTION[7]:                        requested_wpad = 1
      DHCP4.OPTION[8]:                        requested_broadcast_address = 1
      DHCP4.OPTION[9]:                        next_server = 0.0.0.0
      DHCP4.OPTION[10]:                       broadcast_address = 172.30.207.255
      DHCP4.OPTION[11]:                       requested_interface_mtu = 1
      DHCP4.OPTION[12]:                       requested_subnet_mask = 1
      DHCP4.OPTION[13]:                       expiry = 1930410759
      DHCP4.OPTION[14]:                       dhcp_lease_time = 315360000
      DHCP4.OPTION[15]:                       ip_address = 172.30.199.136
      DHCP4.OPTION[16]:                       requested_static_routes = 1
      DHCP4.OPTION[17]:                       dhcp_message_type = 5
      DHCP4.OPTION[18]:                       requested_domain_name_servers = 1
      DHCP4.OPTION[19]:                       requested_nis_servers = 1
      DHCP4.OPTION[20]:                       requested_ntp_servers = 1
      DHCP4.OPTION[21]:                       subnet_mask = 255.255.240.0
      DHCP4.OPTION[22]:                       routers = 172.30.207.253
      DHCP4.OPTION[23]:                       domain_name_servers = 100.100.2.136 100.100.2.138
      DHCP4.OPTION[24]:                       requested_ms_classless_static_routes = 1
      DHCP4.OPTION[25]:                       requested_nis_domain = 1
      DHCP4.OPTION[26]:                       network_number = 172.30.192.0
      DHCP4.OPTION[27]:                       requested_host_name = 1
      DHCP4.OPTION[28]:                       dhcp_server_identifier = 172.30.207.253
      IP6.GATEWAY:                            
  ```

* `RHEL7`系统支持网络会话功能，允许用户在多个配置文件中快速切换，网络会话功能后一切就简单多了—只需在不同的使用环境中激活相应的网络会话，就可以实现网络配置信息的自动切换了。
* nmcli命令并按照“connection add con-name type ifname”的格式来创建网络会话。假设将公司网络中的网络会话称之为company，将家庭网络中的网络会话称之为house，现在依次创建各自的网络会话。
  ```shell
    [root@herhan ~]# nmcli connection add con-name company ifname eth0 autoconnect no type ethernet ip4 172.30.199.136/20 gw4 172.30.207.253
    Connection 'company' (9e701d09-7e6e-47e7-b3e4-f4aeae50cef6) successfully added.
    [root@herhan ~]# nmcli connection add con-name house type ethernet ifname eth0
    Connection 'house' (53aa10f7-c9e3-43f5-bc72-f70bb0323b90) successfully added.
    [root@herhan ~]# nmcli connection show
    NAME         UUID                                  TYPE            DEVICE 
    System eth0  5fb06bd0-0bb0-7ffb-45f1-d6edd65f3e03  802-3-ethernet  eth0   
    company      9e701d09-7e6e-47e7-b3e4-f4aeae50cef6  802-3-ethernet  --     
    house        53aa10f7-c9e3-43f5-bc72-f70bb0323b90  802-3-ethernet  --  
  ```

* `nmcli`命令配置过的网络会话是永久生效的，顺手启用house网络会话，网卡就能自动通过`DHCP`获取到`IP`地址了。
  ```shell
    [root@herhan ~]# nmcli connection up house
    Connection successfully activated (D-Bus active path: /org/freedesktop/NetworkManager/ActiveConnection/1)
    [root@herhan ~]# ifconfig
    eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
            inet 172.30.199.136  netmask 255.255.240.0  broadcast 172.30.207.255
            inet6 fe80::e3f9:29dd:843e:b864  prefixlen 64  scopeid 0x20<link>
            ether 00:16:3e:16:45:8c  txqueuelen 1000  (Ethernet)
            RX packets 36031  bytes 7743870 (7.3 MiB)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 33598  bytes 5108241 (4.8 MiB)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

    lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
            inet 127.0.0.1  netmask 255.0.0.0
            loop  txqueuelen 1  (Local Loopback)
            RX packets 0  bytes 0 (0.0 B)
            RX errors 0  dropped 0  overruns 0  frame 0
            TX packets 0  bytes 0 (0.0 B)
            TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
  ```

### 1.3 绑定两块网卡

* 一般来讲，生产环境必须提供7×24小时的网络传输服务。借助于网卡绑定技术，不仅可以提高网络传输速度，更重要的是，还可以确保在其中一块网卡出现故障时，依然可以正常提供网络服务。

* 第1步：在虚拟机系统中再添加一块网卡设备，请确保两块网卡都处在同一个网络连接中（即网卡模式相同），处于相同模式的网卡设备才可以进行网卡绑定，否则这两块网卡无法互相传送数据。
* 第2步：使用Vim文本编辑器来配置网卡设备的绑定参数。
  * 需要对参与绑定的网卡设备逐个进行“初始设置”。需要注意的是，这些原本独立的网卡设备此时需要被配置成为一块“从属”网卡，服务于“主”网卡，在进行了初始设置之后，它们就可以支持网卡绑定。
  ```shell
    # 查看物理网卡信息
    [root@herhan ~]# nmcli device 
    DEVICE       TYPE      STATE      CONNECTION  
    eno16777736  ethernet  connected  eno16777736 
    eno33554992  ethernet  connected  eno33554992 
    eno50332216  ethernet  connected  eno50332216 
    lo           loopback  unmanaged  --
    # 查看网卡连接信息 
    [root@herhan ~]# nmcli connection show 
    NAME         UUID                                  TYPE            DEVICE      
    eno50332216  36b38556-4bf8-4ce0-8045-4f5465530f67  802-3-ethernet  eno50332216 
    eno33554992  46831ccf-905b-4361-b5ba-c5448133e288  802-3-ethernet  eno33554992 
    eno16777736  613db14a-2375-4a89-b55a-d2abd8fc65d5  802-3-ethernet  eno16777736
    # 删除网卡连接信息
    [root@herhan ~]# nmcli connection delete eno33554992 
    [root@herhan ~]# nmcli connection delete eno50332216 
    [root@herhan ~]# 
    [root@herhan ~]# nmcli connection show 
    NAME         UUID                                  TYPE            DEVICE      
    eno16777736  613db14a-2375-4a89-b55a-d2abd8fc65d5  802-3-ethernet  eno16777736 
    # 网卡连接信息删除成功。
    # 这里删除的其实就是/etc/sysconfig/network-scripts目录下两块网卡的配置文件。
    [root@herhan network-scripts]# pwd
    /etc/sysconfig/network-scripts
    [root@herhan network-scripts]# ls ifcfg-*
    ifcfg-eno16777736  ifcfg-lo
  ```
  * 创建`bond0`
    * `primary eno33554992`：指定主网卡为`eno33554992`   --这里也可以不指定`primary`网卡（备注：子网卡均正常时，`primary`网卡优先处于活跃状态）。
    * `mode active-backup`：指定`bonding`模式为`active-backup`
    * `miimon 100`：以毫秒为单位指定 `MII` 链接监控的频率(默认为0，即关闭此功能；配置`miimon`参数时，最好从`100`开始)。
    * `bonding`内核模块必须在`ifcfg-bondN`中的`BONDING_OPTS="bonding parameters"`中指定，各参数间使用空格分隔，不要在`/etc/modprobe.d/bonding.conf`和已经弃用的`/etc/modprobe.conf`文件中指定`bonding`配置选项。
  ```shell
    [root@herhan network-scripts]# nmcli connection add type bond ifname bond0 con-name bond0 miimon 100 mode active-backup primary eno33554992 ip4 192.168.231.200/24
    Connection 'bond0' (dc55d681-0d5a-4de2-8e94-0e5cb2e249c9) successfully added.
    [root@test03 network-scripts]# cat ifcfg-bond0
    DEVICE=bond0
    BONDING_OPTS="miimon=100 mode=active-backup primary=eno33554992"
    TYPE=Bond
    BONDING_MASTER=yes
    BOOTPROTO=none
    IPADDR=192.168.231.200
    PREFIX=24
    DEFROUTE=yes
    IPV4_FAILURE_FATAL=no
    IPV6INIT=yes
    IPV6_AUTOCONF=yes
    IPV6_DEFROUTE=yes
    IPV6_PEERDNS=yes
    IPV6_PEERROUTES=yes
    IPV6_FAILURE_FATAL=no
    NAME=bond0
    UUID=dc55d681-0d5a-4de2-8e94-0e5cb2e249c9
    ONBOOT=yes
  ```
  * 创建`bond0`子接口
  ```shell
    # 添加网卡eno33554992：设备类型：bond-slave；连接名称：bond0-p1；master：bond0
    [root@herhan network-scripts]# nmcli connection add type bond-slave ifname eno33554992 con-name bond0-p1 master bond0 
    Connection 'bond0-p1' (aaa54dc4-8122-4246-99e8-afd711e3102d) successfully added.
    # 添加网卡eno50332216：设备类型：bond-slave；连接名称：bond0-p2；master：bond0
    [root@herhan network-scripts]# nmcli connection add type bond-slave ifname eno50332216 con-name bond0-p2 master bond0 
    Connection 'bond0-p2' (11e38097-2273-4565-bf1c-3ce2e2e2a0ea) successfully added.
    [root@herhan ~]# cat /etc/sysconfig/network-scripts/ifcfg-bond0-p1 
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=ens32
    MASTER=bond0
    SLAVE=yes
    [root@herhan ~]# vim /etc/sysconfig/network-scripts/ifcfg-bond0-p2
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=ens34
    MASTER=bond0
    SLAVE=yes
  ```

* 第3步：让`Linux`内核支持网卡绑定驱动。常见的网卡绑定驱动有三种模式—`mode0`、`mode1`和`mode6`。下面以绑定两块网卡为例，讲解使用的情景。
  * `mode0`（平衡负载模式）：平时两块网卡均工作，且自动备援，但需要在与服务器本地网卡相连的交换机设备上进行端口聚合来支持绑定技术。
  * `mode1`（自动备援模式）：平时只有一块网卡工作，在它故障后自动替换为另外的网卡。
  * `mode6`（平衡负载模式）：平时两块网卡均工作，且自动备援，无须交换机设备提供辅助支持。
* `Vim`文本编辑器创建一个用于网卡绑定的驱动文件，使得绑定后的`bond0`网卡设备能够支持绑定技术（`bonding`）；同时定义网卡以`mode6`模式进行绑定，且出现故障时自动切换的时间为100毫秒。
  ```shell
    [root@localhost ~]# vim /etc/modprobe.d/bond.conf
    alias bond0 bonding
    options bond0 miimon=100 mode=6
  ```

* 第4步：重启网络服务后网卡绑定操作即可成功。正常情况下只有`bond0`网卡设备才会有`IP`地址等信息：
  ```shell
    [root@herhan ~]# systemctl restart network
  ```

  * 激活`bond0`及其子接口
  ```shell
    # 查看当前已激活的网络接口：
    [root@herhan network-scripts]# nmcli connection show --active 
    NAME         UUID                                  TYPE            DEVICE      
    eno16777736  613db14a-2375-4a89-b55a-d2abd8fc65d5  802-3-ethernet  eno16777736 
    bond0-p2     11e38097-2273-4565-bf1c-3ce2e2e2a0ea  802-3-ethernet  eno50332216 
    bond0-p1     aaa54dc4-8122-4246-99e8-afd711e3102d  802-3-ethernet  eno33554992 
    bond0        dc55d681-0d5a-4de2-8e94-0e5cb2e249c9  bond            bond0
    # 如果bond0-p1、bond0-p2、bond0没有激活，可使用下面命令进行激活：
    [root@herhan network-scripts]# nmcli connection up bond0-p1
    [root@herhan network-scripts]# nmcli connection up bond0-p2
    [root@herhan network-scripts]# nmcli connection up bond0
  ```

  * `bonding`状态查看
  ```shell
    [root@herhan network-scripts]# cd /proc/net/bonding/
    [root@herhan bonding]# ls
    bond0
    [root@herhan bonding]# cat bond0 
    Ethernet Channel Bonding Driver: v3.7.1 (April 27, 2011)

    Bonding Mode: fault-tolerance (active-backup)
    Primary Slave: eno33554992 (primary_reselect always)
    Currently Active Slave: eno33554992
    MII Status: up
    MII Polling Interval (ms): 100
    Up Delay (ms): 0
    Down Delay (ms): 0

    Slave Interface: eno33554992
    MII Status: up
    Speed: 1000 Mbps
    Duplex: full
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:56:69:57
    Slave queue ID: 0

    Slave Interface: eno50332216
    MII Status: up
    Speed: 1000 Mbps
    Duplex: full
    Link Failure Count: 0
    Permanent HW addr: 00:0c:29:56:69:61
    Slave queue ID: 0
  ```
## 2.远程控制服务
### 2.1 配置sshd服务
* `SSH（Secure Shell）`是一种能够以安全的方式提供远程登录的协议，也是目前远程管理`Linux`系统的首选方式。
* `sshd`是基于`SSH`协议开发的一款远程管理服务程序，而且能够提供两种安全验证的方法：
  * 基于口令的验证——用账户和密码来验证登录；
  * 基于密钥的验证——需要在本地生成密钥对，然后把密钥对中的公钥上传至服务器，并与服务器中的公钥进行比较；该方式相较来说更安全。
* `sshd`服务的配置文件位置：`/etc/ssh/sshd_config`
  * `sshd`服务的配置文件中包含的参数以及作用
  
  参数|作用
  -|-
  `Port` 22|默认的`sshd`服务端口
  `ListenAddress` 0.0.0.0|设定`sshd`服务器监听的`IP`地址
  `Protocol` 2|`SSH`协议的版本号
  `HostKey /etc/ssh/ssh_host_key`|`SSH`协议版本为1时，`DES`私钥存放的位置
  `HostKey /etc/ssh/ssh_host_rsa_key`|`SSH`协议版本为2时，RSA私钥存放的位置
  `HostKey /etc/ssh/ssh_host_dsa_key`|`SSH`协议版本为3时，DSA私钥存放的位置
  `PermitRootLogin yes`|设定是否允许`root`管理员直接登录
  `StrictModes yes`|当远程用户的私钥改变时直接拒绝连接
  `MaxAuthTries 6`|最大密码尝试次数
  `MaxSessions 10`|最大终端数
  `PasswordAuthentication yes`|是否允许密码验证
  `PermitEmptyPasswords on`|是否允许空密码登录（很不安全）
* 登录使用`ssh [参数] 主机IP地址`,登出使用`exit`.
  ```shell
    [root@herhan ~]# ssh root@10.0.0.41
    The authenticity of host '10.0.0.41 (10.0.0.41)' can't be established.       
    ECDSA key fingerprint is SHA256:LO0ESvH9o7/G6BDDY0WLRb0a9+3SfU1/c7HL5BzRCH0. 
    ECDSA key fingerprint is MD5:82:cd:09:35:b1:76:95:88:d6:34:aa:ef:b8:ba:5b:c4.
    Are you sure you want to continue connecting (yes/no)? yes
    Warning: Permanently added '10.0.0.41' (ECDSA) to the list of known hosts.
    root@10.0.0.41's password: 
    Last failed login: Tue May 18 05:54:03 CST 2021 on tty1
    There were 2 failed login attempts since the last successful login.
    Last login: Mon May 17 22:56:42 2021 from 10.0.0.1
    [root@herhan ~]# exit
    logout
    Connection to 10.0.0.41 closed.
  ```
### 2.2 安全密钥验证
* 加密是对信息进行编码和解码技术，它通过一定的算法（密钥）将原有可以直接阅读的明文信息转换成密文形式。
* 密钥即是密文的钥匙，有私钥和公钥之分。
* 配置密钥验证方式
  * 1.在客户端主机中生成“密钥对”
    ```shell
      [root@herhan ~]# ssh-keygen
      Generating public/private rsa key pair.
      Enter file in which to save the key (/root/.ssh/id_rsa): 
      Enter passphrase (empty for no passphrase): 
      Enter same passphrase again: 
      Your identification has been saved in /root/.ssh/id_rsa.      
      Your public key has been saved in /root/.ssh/id_rsa.pub.      
      The key fingerprint is:
      SHA256:FRTH/xjPEmt6xzbc2TtwMBOuFDEWsHAaT0ig0gYLEWk root@backup
      The key's randomart image is:
      +---[RSA 2048]----+
      |+=   .o+.==B+    |
      |oE+ .  .B o+o.   |
      |.o +   . o. o..  |
      |  o      . . =+  |
      |        S . . +O |
      |           . .=.+|
      |             o+o+|
      |            . .=*|
      |             . ++|
      +----[SHA256]-----+
    ```
  * 2.把客户端主机中生成的公钥文件传送至远程主机
    ```shell
      [root@herhan ~]# ssh-copy-id 10.0.0.41
      /usr/bin/ssh-copy-id: INFO: Source of key(s) to be installed: "/root/.ssh/id_rsa.pub"
      /usr/bin/ssh-copy-id: INFO: attempting to log in with the new key(s), to filter out any that are already installed
      /usr/bin/ssh-copy-id: INFO: 1 key(s) remain to be installed -- if you are prompted now it is to install the new keys
      root@10.0.0.41's password: 


      Now try logging into the machine, with:   "ssh '10.0.0.41'"
      and check to make sure that only the key(s) you wanted were added.
    ```
  * 3.对服务器进行设置，使其只允许密钥验证，拒绝传统的口令验证方式。记得在修改配置文件后保存并重启sshd服务程序
    ```shell
      [root@herhan ~]# vim /etc/ssh/sshd_config 
      ………………省略部分输出信息………………
      70 # To disable tunneled clear text passwords, change to no here!
      71 #PasswordAuthentication yes
      72 #PermitEmptyPasswords no
      73 PasswordAuthentication no
      74 
      ………………省略部分输出信息………………
      [root@herhan ~]# systemctl restart sshd
    ```
  * 4.在客户端尝试登录到服务器，此时无须输入密码也可成功登录，特别方便：
    ```shell
      [root@herhan ~]# ssh 10.0.0.41
      Last login: Mon Apr 13 19:34:13 2017
    ```
### 2.3 远程传输命令
* `scp(secure copy)`是一个基于`SSH`协议在网络之间进行安全传输的命令。
  * 上传的格式:`scp [参数] 本地文件 远程账户@远程IP地址:远程目录`
  * `scp`命令中可用的参数及作用
    
    参数|作用
    -|-
    `-V`|显示详细的连接进度
    `-P`|指定远程主机的`sshd`端口号
    `-r`|用于传送文件夹
    `-6`|使用IPv6协议
    ```shell
      [root@Client ~]# echo "Welcome to LinuxProbe.Com" > readme.txt
      [root@Client ~]# scp /root/readme.txt 192.168.10.10:/home
      readme.txt                                    100%   26    13.6KB/s   00:00
    ```
  * 下载的格式:`scp [参数] 远程用户@远程IP地址:远程文件 本地目录`
    ```shell
      [root@Client ~]# scp 10.0.0.41:/etc/redhat-release /root
      [root@Client ~]# scp 10.0.0.41:/etc/redhat-release /root
      redhat-release                                100%   45    23.6KB/s   00:00    
      [root@Client ~]# cat redhat-release 
      Red Hat Enterprise Linux release 8.0 (Ootpa)
    ```

## 3.不间断会话服务
* `screen`是一款能够实现多窗口远程控制的开源服务程序，简单来说就是为了解决网络异常中断或为了同时控制多个远程终端窗口而设计的，能够做到实现如下功能：
  * 会话恢复
  * 多窗口
  * 会话共享
### 3.1 管理远程会话
* `screen`常用的参数：
  
  参数|作用
  -|-
  `-S`|参数创建会话窗口
  `-d`|参数将指定会话进行离线处理
  `-r`|参数恢复指定会话
  `-x`|参数一次性恢复所有的会话
  `-ls`|参数显示当前已有的会话
  `-wipe`|参数把目前无法使用的会话删除

* 不再使用某个会话窗口，可以把它设置为临时断开(detach)模式，随后在需要再重新连接(attach)回来即可。
### 3.2 会话共享功能
* `screen`的会话共享功能的流程

![9-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/9-4.png)