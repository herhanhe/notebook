# Linux_day8 Iptables与Firewalled防火墙

===

<!-- TOC -->

- [Linux_day8 Iptables与Firewalled防火墙](#linux_day8-iptables与firewalled防火墙)
  - [1.防火墙管理工具](#1防火墙管理工具)
  - [2.Iptables](#2iptables)
    - [2.1 策略与规则链](#21-策略与规则链)
    - [2.2 基本命令参数](#22-基本命令参数)
  - [3.Firewalld](#3firewalld)
    - [3.1 终端管理工具](#31-终端管理工具)
    - [3.2 图形管理工具](#32-图形管理工具)
  - [4.服务的访问控制列表](#4服务的访问控制列表)

<!-- /TOC -->

## 1.防火墙管理工具

* 防火墙
  * 在公网与企业内网之间充当保护屏障
  * 有软件或硬件之分
  * 但主要功能都是依据策略对穿越防火墙自身的流量进行过滤。
  * 防火墙策略可以基于流量的源目地址、端口号、协议、应用等信息来定制，然后防火墙使用预先定制的策略规则监控出入的流量，若流量与某一条策略规则相匹配，则执行相应的处理，反之则丢弃。
![8-1](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-1.png)

* 防火墙管理工具
  * `iptables`与`firewalld`
    * 只是用来定义防火墙策略的防火墙管理工具
    * `iptables`服务会把配置好的防火墙策略交由内核层面的`netfilter`网络过滤器来处理
    * `firewalld`服务则是把配置好的防火墙策略交由内核层面的`nftables`包过滤框架来处理

## 2.Iptables

### 2.1 策略与规则链

* 防火墙策略读取规则
  * 防火墙会从上至下的顺序来读取配置的策略规则，在找到匹配项后就立即结束匹配工作并去执行匹配项中定义的行为（即放行或阻止）。如果在读取完所有的策略规则之后没有匹配项，就去执行默认的策略。
  * 防火墙策略规则的设置有两种：一种是“通”（即放行），一种是“堵”（即阻止）。当防火墙的默认策略为拒绝时（堵），就要设置允许规则（通），否则谁都进不来；如果防火墙的默认策略为允许时，就要设置拒绝规则，否则谁都能进来，防火墙也就失去了防范的作用。

* `iptables`规则链
  * 服务把用于处理或过滤流量的策略条目称之为规则，多条规则可以组成一个规则链，而规则链则依据数据包处理位置的不同进行分类，具体如下：
    * 在进行路由选择前处理数据包（`PREROUTING`）；
    * 处理流入的数据包（`INPUT`）；
    * 处理流出的数据包（`OUTPUT`）；
    * 处理转发的数据包（`FORWARD`）；
    * 在进行路由选择后处理数据包（`POSTROUTING`）。

* `iptables`服务策略
  * `ACCEPT`（允许流量通过）
  * `REJECT`（拒绝流量通过）
  * `LOG`（记录日志信息）
  * `DROP`（拒绝流量通过）
  * `Linux`系统中的防火墙策略设置为`REJECT`拒绝动作后，流量发送方会看到端口不可达的响应
  * `Linux`系统中的防火墙策略修改成`DROP`拒绝动作后，流量发送方会看到响应超时的提醒

### 2.2 基本命令参数

* `iptables`是一款基于命令行的防火墙策略管理工具
* `iptables`命令可以根据流量的源地址、目的地址、传输协议、服务类型等信息进行匹配，一旦匹配成功，`iptables`就会根据策略规则所预设的动作来处理这些流量。
* 防火墙策略规则的匹配顺序是从上至下的，因此要把较为严格、优先级较高的策略规则放到前面，以免发生错误。

参数|作用
-|-
`-P`|设置默认策略
`-F`|清空规则链
`-L`|查看规则链
`-A`|在规则链的末尾加入新规则
`-I num`|在规则链的头部加入新规则
`-D num`|删除某一条规则
`-s`|匹配来源地址`IP/MASK`，加叹号“!”表示除这个`IP`外
`-d`|区配目标地址
`-i 网卡名称`|匹配从这块网卡流入的数据
`-o 网卡名称`|匹配从这块网卡流出的数据
`-p`|匹配协议，如TCP、UDP、ICMP
`--dport num`|匹配目标端口号
`--sport num`|匹配来源端口号

* 在`iptables`命令后添加`-L`参数查看已有的防火墙规则链
```shell
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination    
```

* 在`iptables`命令后添加`-F`参数清空已有的防火墙规则链：
```shell
  [root@herhan ~]# iptables -F
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination 
```

* 把`INPUT`规则链的默认策略设置为拒绝：
```shell
  [root@herhan ~]# iptables -I INPUT -p icmp -j ACCEPT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         
  ACCEPT     icmp --  anywhere             anywhere     

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination         
```

* 删除`INPUT`规则链中刚刚加入的那条策略（允许`ICMP`流量），并把默认策略设置为允许：
```shell
  [root@herhan ~]# iptables -D INPUT 1
  [root@herhan ~]# iptables -P INPUT ACCEPT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination                   

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination      
```

* 将`INPUT`规则链设置为只允许指定网段的主机访问本机的22端口，拒绝来自其他所有主机的流量：
```shell
  [root@herhan ~]# iptables -I INPUT -s 0.0.0.0/0 -p tcp --dport 22 -j ACCEPT
  [root@herhan ~]# iptables -A INPUT -p tcp --dport 22 -j REJECT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         
  ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:ssh reject-with icmp-port-unreachable

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination      
```

* 向`INPUT`规则链中添加拒绝所有人访问本机12345端口的策略规则：
```shell
  [root@herhan ~]# iptables -I INPUT -p tcp --dport 12345 -j REJECT
  [root@herhan ~]# iptables -I INPUT -p udp --dport 12345 -j REJECT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         
  REJECT     udp  --  anywhere             anywhere             udp dpt:italk reject-with icmp-port-unreachable
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:italk reject-with icmp-port-unreachable
  ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:ssh reject-with icmp-port-unreachable

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination         
```

* 向`INPUT`规则链中添加拒绝192.168.10.5主机访问本机80端口（`Web`服务）的策略规则：
```shell
  [root@herhan ~]# iptables -I INPUT -p tcp -s 192.168.0.5 --dport 80 -j REJECT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         
  REJECT     tcp  --  192.168.0.5          anywhere             tcp dpt:http reject-with icmp-port-unreachable
  REJECT     udp  --  anywhere             anywhere             udp dpt:italk reject-with icmp-port-unreachable
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:italk reject-with icmp-port-unreachable
  ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:ssh reject-with icmp-port-unreachable

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination  
```

* 向`INPUT`规则链中添加拒绝所有主机访问本机1000～1024端口的策略规则：
```shell
  [root@herhan ~]# iptables -A INPUT -p tcp --dport 1000:1024 -j REJECT
  [root@herhan ~]# iptables -A INPUT -p udp --dport 1000:1024 -j REJECT
  [root@herhan ~]# iptables -L
  Chain INPUT (policy ACCEPT)
  target     prot opt source               destination         
  REJECT     tcp  --  192.168.0.5          anywhere             tcp dpt:http reject-with icmp-port-unreachable
  REJECT     udp  --  anywhere             anywhere             udp dpt:italk reject-with icmp-port-unreachable
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:italk reject-with icmp-port-unreachable
  ACCEPT     tcp  --  anywhere             anywhere             tcp dpt:ssh
  REJECT     tcp  --  anywhere             anywhere             tcp dpt:ssh reject-with icmp-port-unreachable
  REJECT     tcp  --  anywhere             anywhere             tcp dpts:cadlock2:1024 reject-with icmp-port-unreachable
  REJECT     udp  --  anywhere             anywhere             udp dpts:cadlock2:1024 reject-with icmp-port-unreachable

  Chain FORWARD (policy ACCEPT)
  target     prot opt source               destination         

  Chain OUTPUT (policy ACCEPT)
  target     prot opt source               destination         
```

* `iptables`执行保存命令
```shell
  [root@herhan ~]# service iptables save
  iptables: Saving firewall rules to /etc/sysconfig/iptables: [ OK ]
```

## 3.Firewalld
* `firewalld`（`Dynamic Firewall Manager of Linux systems`，`Linux`系统的动态防火墙管理器）服务是`RHEL7`系统中默认的防火墙配置管理工具
* 有基于`CLI`（命令行界面）和基于`GUI`（图形用户界面）的两种管理方式。
* `firewalld`支持动态更新技术并加入了区域（`zone`）的概念, firewalld中常用的区域名称及策略规则:

区域|默认规则策略
-|-
`trusted`|允许所有的数据包
`home`|拒绝流入的流量，除非与流出的流量相关；而如果流量与`ssh`、`mdns`、`ipp-client`、`amba-client`与`dhcpv6-client`服务相关，则允许流量
`internal`|等同于`home`区域
`work`|拒绝流入的流量，除非与流出的流量相关；而如果流量与`ssh`、`ipp-client`与`dhcpv6-client`服务相关，则允许流量
`public`|拒绝流入的流量，除非与流出的流量相关；而如果流量与`ssh`、`dhcpv6-client`服务相关，则允许流量
`external`|拒绝流入的流量，除非与流出的流量相关；而如果流量与`ssh`服务相关，则允许流量
`dmz`|拒绝流入的流量，除非与流出的流量相关；而如果流量与`ssh`服务相关，则允许流量
`block`|拒绝流入的流量，除非与流出的流量相关
`drop`|拒绝流入的流量，除非与流出的流量相关

### 3.1 终端管理工具
* `firewall-cmd`是`firewalld`防火墙配置管理工具的`CLI`（命令行界面）版本。
  * `Runtime`模式，又称为当前生效模式，防火墙策略默认为运行时,而且随着系统的重启会失效。
  * `Permanent`模式，永久模式，让配置策略一直存在，方法就是在用`firewall-cmd`命令正常设置防火墙策略时添加`--permanent`参数，这样配置的防火墙策略就可以永久生效了。
* `firewall-cmd`命令中使用的参数以及作用:

参数|作用
`--get-default-zone`|查询默认的区域名称
`--set-default-zone=<区域名称>`|设置默认的区域，使其永久生效
`--get-zones`|显示可用的区域
`--get-services`|显示预先定义的服务
`--get-active-zones`|显示当前正在使用的区域与网卡名称
`--add-source=`|将源自此`IP`或子网的流量导向指定的区域
`--remove-source=`|不再将源自此`IP`或子网的流量导向某个指定区域
`--add-interface=<网卡名称>`|将源自该网卡的所有流量都导向某个指定区域
`--change-interface=<网卡名称>`|将某个网卡与区域进行关联
`--list-all`|显示当前区域的网卡配置参数、资源、端口以及服务等信息
`--list-all-zones`|显示所有区域的网卡配置参数、资源、端口以及服务等信息
`--add-service=<服务名>`|设置默认区域允许该服务的流量
`--add-port=<端口号/协议>`|设置默认区域允许该端口的流量
`--remove-service=<服务名>`|设置默认区域不再允许该服务的流量
`--remove-port=<端口号/协议>`|设置默认区域不再允许该端口的流量
`--reload`|让“永久生效”的配置规则立即生效，并覆盖当前的配置规则
`--panic-on`|开启应急状况模式
`--panic-off`|关闭应急状况模式

* 查看`firewalld`服务当前所使用的区域：
```shell
  [root@herhan ~]# firewall-cmd --get-default-zone
  public
```

* 查询`eth0`网卡在`firewalld`服务中的区域：
```shell
  [root@herhan ~]# firewall-cmd --get-zone-of-interface=eth0
  no zone
```

* 把`firewalld`服务中`eth0`网卡的默认区域修改为`external`，并在系统重启后生效。分别查看当前与永久模式下的区域名称：
```shell
  [root@herhan ~]# firewall-cmd --permanent --zone=external --change-interface=eth0
  success
  [root@herhan ~]# firewall-cmd --get-zone-of-interface=eth0
  no zone
  [root@herhan ~]# firewall-cmd --reload
  success
  [root@herhan ~]# firewall-cmd --get-zone-of-interface=eth0
  external
  [root@herhan ~]# firewall-cmd --permanent --get-zone-of-interface=eth0
  external
```

* 把`firewalld`服务的当前默认区域设置为`public`：
```shell
  [root@herhan ~]# firewall-cmd --set-default-zone=public
  Warning: ZONE_ALREADY_SET: public
  success
  [root@herhan ~]# firewall-cmd --get-default-zone
  public
```

* 启动/关闭`firewalld`防火墙服务的应急状况模式，阻断一切网络连接（当远程控制服务器时请慎用）：
```shell
  [root@herhan ~]# firewall-cmd --panic-on
  success
  [root@herhan ~]# firewall-cmd --panic-off
  success
```

* 查询`public`区域是否允许请求`SSH`和`HTTPS`协议的流量：
```shell
  [root@herhan ~]# firewall-cmd --zone=public --query-service=ssh
  yes
  [root@herhan ~]# firewall-cmd --zone=public --query-service=https
  no
```

* 把`firewalld`服务中请求`HTTPS`协议的流量设置为永久允许，并立即生效：
```shell
  [root@herhan ~]# firewall-cmd --zone=public --add-service=https
  success
  [root@herhan ~]# firewall-cmd --permanent --zone=public --add-service=https
  success
  [root@herhan ~]# firewall-cmd --reload
  success
```

* 把`firewalld`服务中请求`HTTP`协议的流量设置为永久拒绝，并立即生效：
```shell
  [root@herhan ~]# firewall-cmd --permanent --zone=public --remove-service=http
  Warning: NOT_ENABLED: http
  success
  [root@herhan ~]# firewall-cmd --reload
  success
```

* 把在`firewalld`服务中访问8080和8081端口的流量策略设置为允许，但仅限当前生效：
```shell
  [root@herhan ~]# firewall-cmd --zone=public --add-port=8080-8081/tcp
  success
  [root@herhan ~]# firewall-cmd --zone=public --list-port
  8080-8081/tcp
```

* 把原本访问本机888端口的流量转发到22端口，要且求当前和长期均有效：
  * 流量转发命令格式为`firewall-cmd --permanent --zone=<区域> --add-forward-port=port=<源端口号>:proto=<协议>:toport=<目标端口号>:toaddr=<目标IP地址>`
```shell
  [root@herhan ~]# firewall-cmd --permanent --zone=public --add-forward-port=port=888:proto=tcp:toport=22:toaddr=192.168.10.10
  success
  [root@herhan ~]# firewall-cmd --reload
  success
```

* `firewalld`中的富规则表示更细致、更详细的防火墙策略配置，它可以针对系统服务、端口号、源地址和目标地址等诸多信息进行更有针对性的策略配置。它的优先级在所有的防火墙策略中也是最高的
* 我们可以在`firewalld`服务中配置一条富规则，使其拒绝192.168.10.0/24网段的所有用户访问本机的`ssh`服务（22端口）：
```shell
  [root@herhan ~]# firewall-cmd --permanent --zone=public --add-rich-rule="rule family="ipv4" source address="192.168.10.0/24" service name="ssh" reject"
  success
  [root@herhan ~]# firewall-cmd --reload
  success
  [root@herhan ~]# firewall-cmd --list-all
  You're performing an operation over default zone ('public'),
  but your connections/interfaces are in zone 'external' (see --get-active-zones)
  You most likely need to use --zone=external option.

  public
    target: default
    icmp-block-inversion: no
    interfaces: 
    sources: 
    services: dhcpv6-client https ssh
    ports: 
    protocols: 
    masquerade: no
    forward-ports: port=888:proto=tcp:toport=22:toaddr=192.168.10.10
    sourceports: 
    icmp-blocks: 
    rich rules: 
          rule family="ipv4" source address="192.168.10.0/24" service name="ssh" reject
```

### 3.2 图形管理工具
* `firewall-config`，它是`firewalld`防火墙配置管理工具的`GUI`（图形用户界面）版本，几乎可以实现所有以命令行来执行的操作:

![8-2](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-2.png)

  * 1：选择运行时（Runtime）模式或永久（Permanent）模式的配置。
  * 2：可选的策略集合区域列表。
  * 3：常用的系统服务列表。
  * 4：当前正在使用的区域。
  * 5：管理当前被选中区域中的服务。
  * 6：管理当前被选中区域中的端口。
  * 7：开启或关闭SNAT（源地址转换协议）技术。
  * 8：设置端口转发策略。
  * 9：控制请求icmp服务的流量
  * 10：管理防火墙的富规则。
  * 11：管理网卡设备。
  * 12：被选中区域的服务，若勾选了相应服务前面的复选框，则表示允许与之相关的流量。
  * 13：firewall-config工具的运行状态。

* `SNAT`（`Source Network Address Translation`，源网络地址转换）技术。`SNAT`是一种为了解决`IP`地址匮乏而设计的技术，它可以使得多个内网中的用户通过同一个外网`IP`接入`Internet`。
  * 没有使用`SNAT`技术，则互联网中的网站服务器在收到`PC`的请求数据包，并回送响应数据包时，将无法在网络中找到这个私有网络的`IP`地址，所以`PC`也就收不到响应数据包了。由于网关服务器应用了`SNAT`技术，所以互联网中的网站服务器会将响应数据包发给网关服务器，再由后者转发给局域网中的`PC`。
  
  ![8-3](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-3.png)
  
  ![8-4](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-4.png)
  
  * 使用`iptables`命令实现`SNAT`技术是一件很麻烦的事情，但是在`firewall-config`中,选中`Masquerade zone`复选框，就自动开启了`SNAT`技术。
  
  ![8-5](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-5.png)

* 配置端口转发
  ![8-6](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-6.png)

* 配置防火墙富规则策略
  ![8-7](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-7.png)

* 把网卡与防火墙策略区域进行绑定
  ![8-8](https://heh-1300576495.cos.ap-chengdu.myqcloud.com/assets/Linux/8-8.png)

## 4.服务的访问控制列表
* `TCP Wrappers`是`RHEL 7`系统中默认启用的一款流量监控程序，它能够根据来访主机的地址与本机的目标服务程序作出允许或拒绝的操作。
  * `Linux`系统中其实有两个层面的防火墙，第一种是前面讲到的基于`TCP/IP`协议的流量过滤工具，而`TCP Wrappers`服务则是能允许或禁止`Linux`系统提供服务的防火墙，从而在更高层面保护了`Linux`系统的安全运行。
* `TCP Wrappers`服务的防火墙策略由两个控制列表文件所控制，
  * 编辑允许控制列表文件（`/etc/hosts.allow`）来放行对服务的请求流量，
  * 编辑拒绝控制列表文件（`/etc/hosts.deny`）来阻止对服务的请求流量。
  * 控制列表文件修改后会立即生效，系统将会先检查允许控制列表文件，如果匹配到相应的允许策略则放行流量；如果没有匹配，则去进一步匹配拒绝控制列表文件，若找到匹配项则拒绝该流量。如果这两个文件全都没有匹配到，则默认放行流量。
  * `TCP Wrappers`服务的控制列表文件中常用的参数:
  
  客户端类型|示例|满足示例的客户端列表
  -|-|-
  单一主机|192.168.10.10|`IP`地址为192.168.10.10的主机
  指定网段|192.168.10.|`IP`段为192.168.10.0/24的主机
  指定网段|192.168.10.0/255.255.255.0|`IP`段为192.168.10.0/24的主机
  指定`DNS`后缀|`.baidu.com`|所有`DNS`后缀为`.baidu.com`的主机
  指定主机名称|`www.baidu.com`|主机名称为`www.baidu.com`的主机
  指定所有客户端|`ALL`|所有主机全部包括在内

  * 配置`TCP Wrappers`服务时需要遵循两个原则：
    * 1. 编写拒绝策略规则时，填写的是服务名称，而非协议名称；
    * 2. 建议先编写拒绝策略规则，再编写允许策略规则，以便直观地看到相应的效果。

* 禁止访问本机`sshd`服务的所有流量
```shell
  [root@herhan ~]# vim /etc/hosts.deny 
  #
  # hosts.deny    This file contains access rules which are used to
  #               deny connections to network services that either use
  #               the tcp_wrappers library or that have been
  #               started through a tcp_wrappers-enabled xinetd.
  #
  #               The rules in this file can also be set up in
  #               /etc/hosts.allow with a 'deny' option instead.
  #
  #               See 'man 5 hosts_options' and 'man 5 hosts_access'
  #               for information on rule syntax.
  #               See 'man tcpd' for information on tcp_wrappers
  #
  sshd:*
```

* 放行源自192.168.10.0/24网段，访问本机`sshd`服务的所有流量
```shell
  [root@herhan ~]# vim /etc/hosts.allow
  #
  # hosts.allow   This file contains access rules which are used to
  #               allow or deny connections to network services that
  #               either use the tcp_wrappers library or that have been
  #               started through a tcp_wrappers-enabled xinetd.
  #
  #               See 'man 5 hosts_options' and 'man 5 hosts_access'
  #               for information on rule syntax.
  #               See 'man tcpd' for information on tcp_wrappers
  sshd:192.168.10
```