# Linux_day8 Iptables与Firewalled防火墙

===

## 1.防火墙管理工具

* 防火墙
  * 在公网与企业内网之间充当保护屏障
  * 有软件或硬件之分
  * 但主要功能都是依据策略对穿越防火墙自身的流量进行过滤。
  * 防火墙策略可以基于流量的源目地址、端口号、协议、应用等信息来定制，然后防火墙使用预先定制的策略规则监控出入的流量，若流量与某一条策略规则相匹配，则执行相应的处理，反之则丢弃。

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

```