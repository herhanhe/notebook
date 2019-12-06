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
* 