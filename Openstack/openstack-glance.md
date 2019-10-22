---
title: Glance
tags: 学习
notebook: OpenStack
---
# `Glance`
---
![44](https://ae01.alicdn.com/kf/He1f104746ef745d49807e2dc716b62f6Z.png)
## `Glance`功能和架构
* `Glance`功能和架构
    * 作为独立的大规模镜像查找服务，为`OpenStack`提供了虚拟机镜像的查找服务
    * `glance`遵循以下设计思想
        * 基于组件的架构-便于快速增加新特性
        * 高可用性-支持大负荷
        * 开放标准-对社区驱动的`API`提供参考实现
    * 主要由三个部分构成：`glance-api`，`glance-registry`和`image datastore`
        * `glance-api`:接收`REST API`请求，与其他两个模块配合，完成诸如镜像的查找、获取、上传、删除等操作，默认监听端：9292
        * `glance-registry`:用于与数据库交互，上存储或获取镜像的元数据（`metadat`）,对应数据库中两张表：
            * `image`表：保存了镜像格式、大小等信息；
            * `image property`表：保存镜像的定制化信息，比如`--property hypervisor_type=qemu`
        * `image store`:存储接口抽象层
            * `Amazon S3`
            * `GlusterFS`
            * `Swift`
            * `Sheepdog`
            * `ceph` 
    * `glance`的架构
    ![45](https://ae01.alicdn.com/kf/Hd33e7a87ad59421abab43ecc30ad4605v.png)
        * 通过`glance`，`OpenStack`的三个模块计算组件（`nova`）、镜像管理组件（`glance`）、存储组件（`swift`，`ceph`，`sheepdog`等）被连接成一个整体，`Glance`为`Nova`提供镜像的查找等操作，而存储组件又为`Glance`提供了实际的存储服务。而`Swift`，`ceph`，`gluster`，`sheepdog`等又是`Glance`存储接口的一些具体实现，`Glance`的存储接口还能支持`S3`等第三方的商业组件
## `Glance`常用操作
* `Glance`支持的操作（`REST API operation`）
    * `create`：创建镜像
    ```shell
        glance image-create --name="CirrOS 0.3.1" --disk-format=qcow2 --container-format=base --visibility=public
    ```
    * `upload`：上传镜像
    ```shell
        glance image-upload ${IMG_ID} < /root/images/cirros-0.3.1-x86_64-disk.img
    ```
    * `download`：下载镜像
    ```shell
        glance image-download ${IMG_ID} > /root/images/${IMG_ID}
    ```
    * `show`：查询镜像详细信息
    ```shell
        glance image-show ${IMG_ID}
    ```
## 问题
* 问题1：packstack搭建环境镜像上传一直`queue`的问题
    * 解决方法：
        * 在`/etc/glance/glace-api.conf`中，修改如下配置项的值为：`notifier_strategy = noop`
* 问题2：默认加载所有的后端存储驱动，但是我们可能只配一种后端存储，所有在`/var/log/glance/api.log`中会报很多`warning`
    * 解决方法：
        * 忽略即可