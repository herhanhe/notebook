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
    [root@herhan ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens32
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=ens32
    MASTER=bond0
    SLAVE=yes
    [root@herhan ~]# vim /etc/sysconfig/network-scripts/ifcfg-ens34
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=ens34
    MASTER=bond0
    SLAVE=yes
```
  * 需要将绑定后的设备命名为bond0并把IP地址等信息填写进去，这样当用户访问相应服务的时候，实际上就是由这两块网卡设备在共同提供服务。
```shell
    [root@localhost ~]# vim /etc/sysconfig/network-scripts/ifcfg-bond0
    TYPE=Ethernet
    BOOTPROTO=none
    ONBOOT=yes
    USERCTL=no
    DEVICE=bond0
    IPADDR=172.24.1.130
    PREFIX=24
    DNS=172.24.1.1
    NM_CONTROLLED=no
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

第4步：重启网络服务后网卡绑定操作即可成功。正常情况下只有`bond0`网卡设备才会有`IP`地址等信息：
```shell
    [root@herhan ~]# systemctl restart network
    [root@herhan ~]# ifconfig
```
