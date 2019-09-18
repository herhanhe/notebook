---
title: OpenStack 概念
tags: 学习
notebook: OpenStack
---
# OpenStack 概念
---
## OpenStack 基本概念

### `Tenant`  ->  租户
* 资源的集合，资源的容器，资源的拥有者是租户
    * 计算资源（物理机），存储资源（云硬盘），网络资源，镜像资源等等
    * 早期的版本中叫`project`
    * `Devstack`默认创建两个`tenant:admin`和`demo`
* `Tenant`的配额（`tenant quotas`）
    * `instance`（实例）个数
    * `vcpu`个数
    * 内存数量
    * 内部`ip`和公网`ip`数量
![1.png](https://ae01.alicdn.com/kf/H704153e1e88c4a67a1babc2448418452W.png)

### `User`    ->  用户
* 认证的对象
    * 访问`openstack`中的每一个服务，都是`user`这个对象去访问的
    * `user`要归属于某个`tenant`，默认生成`admin`和`demo`两个`user`
    * `AWS EC2`没有`tenant`的概念，只支持`user`
![2.png](https://ae01.alicdn.com/kf/H816dd4eac8d0454494ac2aac86da8951S.png)

### `role`    ->  角色
* 权限的集合，将权限赋给用户（`role` -> `user`）
    * `_member_`代表权限在`tenant`内，`admin`代表在所有`tenant`都有权限
    * `role`是可嵌套的，语法格式为：`rule:[result]`
        * `rule`表明这条规是啥，对应一个`action`,以`scope:action`的形式出现
        * `result`代表`rule`的判定结果
![3.png](https://ae01.alicdn.com/kf/H7669b510043e4360905034cae8948c69C.png)

### `service` ->  服务
* Openstack包含的每个模块都是一个服务
    * 对外提供`REST API - REpresentational State Transfer`(表述性状态转移)
        * 如何正确的、统一的使用web标准
        * 将所有对象定义为唯一的ID
        * 将所有对象关联在一起
        * 使用标准方法
        * 资源多重表述
        * 无状态通信（或交互）
![4.png](https://ae01.alicdn.com/kf/H1ed4f2f27ed74d58abcee83f2fca74e0e.png)

### `endpoint`  ->  服务的网络地址
![5.png](https://ae01.alicdn.com/kf/Hffe8e2359b134dacbb968b6cb5376186g.png)
![6.png](https://ae01.alicdn.com/kf/H370cdc4267b8427a943d616b257a9e6aW.png)

## OpenStack高级概念

### `Region`    ->  分区
* 地理上的概念，每个`region`有自己独立的`endpoint`,一个独立的数据中心
* `regions`之间完全隔离，但是多个`regions`之间共享同一个`keystone`和`dashboard`
* 除了提供隔离的功能，`region`的设计更多侧重地理位置的概念，用户可以选择离自己更近的`region`来部署自己的服务。
* `AWS`(亚马逊)最新提出`region`的概念
* 容错能力和可靠性 
### `Availability Zone`
* `az`是在`region`范围内的再次切分，例如可以把一个机架上的机器划分在一个`az`中，划分`az`是为了提高容灾性和提供廉价的隔离服务。（机器独立供电）
* 选择不同的`region`主要考虑哪个`region`靠近你的用户群体，比如用户在美国，自然会选择离美国近的`region`；
* `az`主要是通过冗余来解决可用性问题，在亚马逊的声明中，`instance`不可用指的是用户所有`AZ`中的同一`instances`都不可用
* `az`是用户可见的一个概念，用户在创建`instance`的时候可以选择穿件到哪些`AZ`中
![7.png](https://ae01.alicdn.com/kf/H792c759dd4a04263bd267e65522c9c57D.png)
![8.png](https://ae01.alicdn.com/kf/H495313d6d75c4a21802e6ebf56862e52Q.png)
![9.png](https://ae01.alicdn.com/kf/Hfa3fdb7856b34d9f98d3e23388d301d19.png)

### `Host Aggreates`
* 一组节点的组合，但强调这组节点具有共同的属性
    * `cpu`是制定类型的一组节点，`disks`是`ssd`的一组节点，`os`是`Linux`或`windows`
* 用户不可见的概念，主要用来给`nova-scheduler`调度算法使用
    * 数据库服务的`instances`都是调用到具有ssd属性的`Host Aggreates`中
    * 某个`flavor`或某个`image`的`instance`调度到同一个`Host Aggreates`中

### `Cell`
* 主要用来解决`openstack`的扩展性和规模瓶颈
* 每个`cell`有自己独立的`DB`和`AMQP`
    * `openstack`是由很多的组件通过松耦合构成，当达到一定的规模后，某些模块必然成为整个系统的瓶颈，比较典型的组件就是`database`和`AMOP`
* `cell`实现为树形结构，自然而然引入了分级调度的概念
* 通过在每级`cell`引入`nova-cell`服务，实现了以下功能
    * Messages的路由，即父`cell`通过`nova-cell`将Messages路由到子`cell`的`AMQP`模块
    * 分级调度功能，即调度某个`instances`的时候先要进行cell的选择，支持`filter`和`weighing`的调度策略
    * 资源统计，子`cell`定时的将自己的资源信息上报给父`cell`,用来给分级调度策略提供决策数据和基于`cell`的资源监控
    * `cell`之间的通信（通过`rpc`完成）
![10.png](https://ae01.alicdn.com/kf/Hff15e0cbf61a4f3dbb08dc41e692aa77e.png)
![11.png](https://ae01.alicdn.com/kf/H7f6859da29f14e2c9e5b190e98f25fb4q.png)
### 参考资料
[OpenStack Availability Zone 和 Aggreates Hosts 理解](http://blog.chinaunix.net/uid-20940095-id-3875022.html)
[OpenStack重要概念理解（Regions+Cells+Availability Zones+Host Aggregates等）](http://blog.chinaunix.net/uid-20940095-id-4064233.html)
[OpenStack Keystone的基本概念理解](https://www.cnblogs.com/yuki-lau/archive/2013/01/04/2843918.html)

