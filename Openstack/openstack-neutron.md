---
title: Neutron
tags: 学习
notebook: OpenStack
---
# Neutron

* 网络拓扑
![46](https://pic2.superbed.cn/item/5de8c19ff1f6f81c50c63b2d.png)
## Neutron基本概念
* 网络（network）
  * `Neutron`网络目的是划分物理网络，在多租户环境下提供给每个租户独立的网络环境。`Neutron`中“网络”是一个可以被用户创建的对象，如果要和物理环境下的概念映射的话，这个对象相当于一个巨大的交换机，可以拥有无限多个动态可创建和销毁的虚拟端口。
* 子网（subnet）
  * 子网是由一组`IP`地址组成的地址池。不同子网间的通信需要路由器的支持，`Neutron`中子网隶属于网络。
* 端口（port）
  * 一个端口代表了在逻辑网络交换机上的虚拟交互端口，虚拟机的虚拟接口（可以理解成网卡）连接到这些端口上，端口上有相应的`MAC`和`IP`地址。当给端口分配`IP`地址，需要将端口关联到子网中，从子网的地址池取出`IP`地址。
* 路由器（router）
  * 和物理环境下的路由器类似，`Neutron`中的路由器也是一个路由选择和转发部件。只不过在`Neutron`中，它是可以创建和销毁的软部件。
* `DHCP`
  * 为租户网络提供`DHCP`服务，即`IP`地址动态分配，另外还会提供`metadata`请求服务。
  * `DHCP agent`
    * 负责处理`dhcp`请求，为网络分配`IP`
  * `DHCP agent scheduler`
    * 负责`DHCP agent`与`network`的调度

## 基本命令
* 网络（network）
```shell
    neutron net-create --tenant-id $tenant net1                                     --provider:network vlan 
                       --provider:physical_network physnet1 --provider:segmentation_id 100
``` 
* 子网（subnet）
```shell
    neutron subnet-create --tenant-id $tenant 
                          --name net1_subnet1 net1 192.168.100.0/24
```
* 端口（port）
  * port一般都是自动创建的，很少用户手动创建

## Devstack自动化部署
* OVS+VLAN