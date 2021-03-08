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