---
title: Nova
tags: 学习
notebook: OpenStack
---
# `Nova`
---
## 搭建本地的`pip`源
* 《[基于`CentOS`的`pip`本地源搭建方法](http://blog.chinaunix.net/uid-20940095-id-4303903.html)》
    * 采用bandsnatch与pypi官方源同步，不能指定单个软件包同步
    * bandsnatch仅支持与https的源同步，不支持与http的源同步
    * 同步的软件数量巨大，耗时长，且网络质量差，经常超时失败
* 《[搭建本地`pypi`源方法 – 仅同步`openstack`依赖的的`pypi`软件包](https://blog.csdn.net/wjciayf/article/details/53518622)》
    * 采用pip2pi进行同步，支持单个软件包同步，也支持批量软件包同步
    * 既支持与https的源同步，也支持与http的源同步
    * 同步的软件数量少，耗时短，并且可以与国内优秀的源同步（比如豆瓣）

## `Nova`架构介绍
### 简单架构
* 简单架构
    * 单点服务
    * 无负载均衡
    * 无高可靠
 
 ![16](https://ae01.alicdn.com/kf/Hf535fd82f727425a85fd5aa8bf6b3b1cY.png)
### 复杂架构
 * 复杂架构
    * 负载均衡
    * 高可靠

![17](https://ae01.alicdn.com/kf/H05b24ae651ff41d48e67ebf118d87ac3S.png)

## `nova`的服务
### `Nova API`
* `nova-api`服务
    * 接收和响应用户的`API`请求
        * api接口调用，与命令行调用相区别
```shell
    #API接口：
    curl -H "X-Auth-Token: <Token ID>" http://192.168.100.70:8774/v2/<Tenant ID>/servers
    #命令行：
    nova list
```
* 服务启动脚本（`devstack` vs. `packstack`）
    * `/usr/bin/nova-api  vs.  /etc/init.d/openstack-nova-api ` (对比二者的内容)
*  服务监听端口（`netstat -anp | grep pid`,   `pid`是`nova-api`主进程的`id`）
    * 三个端口： `8773`,`8774`和`8775`

![18](https://ae01.alicdn.com/kf/Hfbfec658921641c7acc5e34c5c53d9d9Q.png)
* 扩展性
    * 服务入口是否会成为瓶颈，比如单个服务无法处理大量并发请求

![19](https://ae01.alicdn.com/kf/H1b81989523d44c7297105077323f4a79S.png)
* `ps aux | grep nova-api` （`devstack`方式部署）
    * 发现启动13个`nova-api`进程，其中一个主进程，实际为12个`nova-api`服务进程
* 启动多少个`nova-api`服务进程由谁来控制
    * `nova.conf`中的默认配置（`devstack`方式部署）
![20](https://ae01.alicdn.com/kf/H89707a4f34af4f1b9b4989e514ac5f83L.png)
    * `nova.conf`中的默认配置（`packstack`方式部署）
![21](https://ae01.alicdn.com/kf/H1316397457fb425d842bc7100f14f4b3R.png)
* `devstack`方式部署时`nova.conf`的自动生成
    * `devstack/lib/nova`中的函数`create_nova_conf`
![22](https://ae01.alicdn.com/kf/Ha469130d1993462786a03c87cf9c3082x.png)
* 三个配置项必须要 >= 1
  * 思考（作业1）：在`packstack`部署的时候，`ec2_workers`和`metadata_workers`都没有配置,前面看到，`ec2_workers=24`, 那么此时有多少个`nova-api`服务进程?

### `Nova-Scheduler`
* `nova-scheduler`服务 – 调度器
    * 虚拟机调度，即创建虚拟机时，根据物理机的资源使用情况，虚拟机应该被分配到哪台物理机上运行
    * 服务启动（`devstack` vs. `packstack`）
    ```shell
        /usr/bin/nova-scheduler --config-file /etc/nova/nova.conf
        /etc/init.d/openstack-nova-scheduler start #（看下启动脚本） 
    ```
    * 监听端口（`ps aux | grep pid`）
        * `nova-scheduler`没有固定的端口
        * `nova-scheduler`只对内部组件提供服务，并不对外服务
    * 调度算法，即选出最佳物理机的算法，主要包括过滤算法和权重算法两个阶段
    ```
        scheduler_driver = nova.scheduler.filter_scheduler.filterScheduler
    ```
    * 目前支持的过滤算法（23种左右）,默认加载以下算法：
        * `RetryFilter`  
        * `AvailabilityZoneFilter`  
        * `RamFilter`  
        * `ComputeFilter`  
        * `ComputeCapabilitiesFilter`  
        * `ImagePropertiesFilter`  
        * `CoreFilter`

        ![23](https://ae01.alicdn.com/kf/H09b567f4cd884fa3bd7cb53bb04cb2c6w.png)

    * 过滤算法
        * 选择，`yes or no?`
        * 符合条件的`host`进入下一轮
    * 权重算法
        * 排序
        * 符合条件的`host`进行优先级排序
    
    ![24](https://ae01.alicdn.com/kf/H02889f1ce42748c0a5a1e71849cb9bd0w.png)
    ![25](https://puui.qpic.cn/fans_admin/0/3_1218999906_1571036384161/0)
    * 过滤（`filter`）算法
        * `RetryFilter`
            * 如果虚拟机调度失败，是否重新调度的次数（ `scheduler_max_attempts=3` ）
        * `AvailabilityZoneFilter`
            * 是否支持虚拟机选择`availability zone`(可用区)
        * `RamFilter`
            * 选择能够满足虚拟机内存要求的物理机，如果不指定默认，不判断内存资源
            * 指定内存超分比率，`ram_allocation_ratio=1.5`8773`
        * `ComputeCapabilitiesFilter`
            * 选择具有某些特定属性的物理机
            * 以`namespace:key`的格式来执行属性，比如`AZ1:node1`
        * `ImagePropertiesFilter`
            * 根据镜像中指定的某一属性（`properties`）来过滤物理机
            ```
                glance image-update img-uuid --property architecture=arm --property hypervisor_type=qemu 

            ```
            * 以这个镜像创建虚拟机，只能选择体系结构式`arm，hypervisor`是`qemu`的主机
        * `CoreFilter`
            * 选择哪些能够满足虚拟机`cpu`要求的那些物理机
            * 如果不设置，单个虚拟机的和数可以超过物理机的和数
            * 可以指定`cpu`超分比率， `cpu_allocation_ratio=16.0`
            * 思考：如果一台4核的物理机，`cpu_allocation_ratio=2`，那么最大可以创建多少核的虚拟机
    * 权重（`weight`）算法-权重是如何计算出来的?
        * `ram_weight_multiplier`(内存权重系数)
            * 整数，选出内存最大的物理机 $->$ 虚拟机在所有物理机上平铺（`spreading`）
            * 负数，选出内存最小的物理机 $->$ 虚拟机在部分物理机上堆叠（`stacking`）
        * `scheduler_host_subset_size`(最佳机器的子集大小)
            *  如果有`N`个机器的优先级相同，则随机选择`scheduler_host_subset_size`个
            *  `scheduler_host_subset_size = 1`（默认）
        * `scheduler_weight_classes`（权重计算算法选择类）
            * `RamWeigher`，内存权重排序算法，默认算法
            * `MetricsWeigher`，自定义的权重排序算法
### `Nova-conductor`
* 数据库访问代理服务
    * 防止计算节点直接访问数据库
        * 计算节点相对不可信、高风险的
        * 负载均衡，可扩展性好
    * 不能与`nova-compute`部署在同一节点上
    * 如果不想使用`nova-conductor`，在`nova.conf`中增加配置项：
        * `use_local = True`
### `Nova-VNC`服务
* `VNC`访问方式原理
    * `VNC`访问方式
        * `host_ip:port`,比如`221.130.253.135:1`
        * 如何获得虚拟机对应的`vnc port`? -> `vncdisplay domain_id`
        * 客户端软件：`vnc viewer, tightVNC`等等
    * `VNC`访问原理
        * 基于`RFB`协议，即`Remote FrameBuffer protocol`
        * 有两部分组成：`vnc server`和`vnc client`
        * `Server`和`client`端共享图形界面 
* `noVNC`访问方式原理
    * 由两个服务构成：`nova-novncproxy`和`nova-consoleauth`
    * `nova-novncproxy`的功能
        * 将公网（`public network`）和私网（`private network`）隔离
            * `vnc client`运行在公网上，`vnc server`运行在私网上
            * `vnc proxy`作为连接二者的桥梁
        * 通过`token`对`vnc client`进行验证
        * 可以同时支持多种`vnc client`
            * `novnc`,基于`html5 websockets`,`Canvas`和`JavaScripts`实现
            * `Spice`,redhat的虚拟桌面技术
    * `nova-consoleauth`服务
        * 用于进行`token`的验证
    * 访问原理
        * `nova-novncproxy`监听`6080`端口
        * 作为用户`vnc`访问的一道大门，处理用户的`vnc`访问请求
* `noVNC`访问原理
![26](https://ae01.alicdn.com/kf/H811b86c152864e638ef4e45b0f47b3a3W.png)
    1. 一个用户试图从浏览器里面打开连接到虚拟机的`VNC Client`
    2. 浏览器向`nova-api`发送请求，要求返回访问`vnc`的`url`
    3. `nova-api`调用`nova-compute`的`get_vnc_console`方法，要求返回连接`VNC`的信息
    4. `nova-compute`调用`libvirt`的`get_vnc_console`函数
    5. `libvirt`会通过解析虚拟机的的配置文件`/instance-00000001.xml`来获得`VNC Server`的信息
    6. `libvirt`将`host`, `port`等信息以`json`格式返回给`nova-compute`
    7. `nova-compute`会随机生成一个`UUID`作为`Token`
    8. `nova-api`会调用`nova-consoleauth`的`authorize_console`函数
    9. `nova-api`将`connect_info`中的`access url`信息返回给浏览器
    10. 当浏览器试图打开这个链接时，会将请求发送给`nova-novncproxy`
    11. `nova-novncproxy`调用`nova-consoleauth`的`check_token`函数
    12. `nova-consoleauth`验证了这个`token`，将这个`instance`对应的`connect_info`返回给`nova-novncproxy`
    13. `nova-novncproxy`通过`connect_info`中的`host`, `port`等信息，连接`compute`节点上的`VNC Server`，从而开始了`proxy`的工作
    14. `nova-compute`将`libvirt`返回的信息以及配置文件中的信息综合成`connect_info`返回给`nova-api`
* `noVNC`配置项
```
    vncserver_proxyclient_address = compute_ip
    vncserver_listen = 0.0.0.0
    vnc_enabled = true
    novncproxy_base_url = http://10.0.10.10:6080/vnc_auto.html
```
### `Nova-cert`和`Nova-objectstore`
* `nova-cert`服务
    * `X509`认证服务，仅用于调用`openstack`兼容`EC2`的`API`时候使用
* `nova-objectstore`服务
    * `nova`对象存储接口服务，通过此服务来访问`Swift`或`S3`
### `Nova-compute`
* `nova-compute`服务 
    * 所有的计算节点要运行的服务
    * 主要处理虚拟机相关的各种操作，虚拟机的创建，删除，挂起，重启等
    * 可以通过`nova service-list`来查看当前环境中的所有的`nova`服务
## 虚拟机创建流程分析
* `openstack`集群的初始状态
![27](https://ae01.alicdn.com/kf/H7cbee27803bd443d9d4146d01540f84bd.png)
* 1.获取用户凭证并将`HTTP`请求发送到`Keystone`
![28](https://ae01.alicdn.com/kf/H16615861706b4a59b2c45516985bf101A.png)
* 2.生成并发送回用于`nova-api`的身份验证令牌
![29](https://ae01.alicdn.com/kf/Ha9984dc9b8d94443bc7b41c9bcf8f160H.png)
* 3.将以“创建实例”形式指定的新实例参数转换为`REST API`请求并调用`nova-api`
![30](https://ae01.alicdn.com/kf/H6436f08d87bf4d5a98d95bebe152efc6D.png)
* 4.验证身份验证令牌和访问权限
![31](https://ae01.alicdn.com/kf/Ha1dc89677a6e4d52b9120b2b1ddffdc08.png)
* 5.验证新实例参数并为新实例创建初始数据库条目
![32](https://ae01.alicdn.com/kf/H4b6e89328e0a4bb59630dd89019dcb73m.png)
* 6.rpc.call. 请求实例计划。期望获取指定了主机`ID`的更新实例条目
![33](https://ae01.alicdn.com/kf/H6e27a16f731348158b0f1acba6d5eebeq.png)
* 7.通过过滤和加权找到合适的主机使用主机ID更新实例条目
![34](https://ae01.alicdn.com/kf/Ha301f9f1a3f54bf79ef5243b071bcf2dq.png)
* 8.rpc.cast 将“虚拟机配置”请求发送到已计划配置的主机上的`nova-compute`
![35](https://ae01.alicdn.com/kf/H650ce71a77724729ad2bbb10c90289caJ.png)
* 9.rpc.call 为实例保留和分配网络
![36](https://ae01.alicdn.com/kf/Hbd39db9874cb478e8bc0ab22d229e39aY.png)
* 10.从数据库中获取实例信息，为虚拟机监控程序驱动程序生成日期并在虚拟机监控程序上执行请求（通过`api`或`libvirt`）
![37](https://ae01.alicdn.com/kf/Hd510a50dc71b4889ad35601c2f6cb6988.png)
* 11.从`Glance`中按镜像ID获取镜像URL并从镜像存储中上传镜像
![38](https://ae01.alicdn.com/kf/H9ad10e90c8214237bac450a17129bfeeO.png)
* 12.轮询请求实例状态
![39](https://ae01.alicdn.com/kf/Hcbfe4f5dd7ee4b728d12daa7ad5b138cM.png)