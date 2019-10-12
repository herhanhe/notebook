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

## Nova架构介绍
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
                选择哪些能够满足虚拟机cpu要求的那些物理机
如果不设置，单个虚拟机的和数可以超过物理机的和数
可以指定cpu超分比率， cpu_allocation_ratio=16.0
思考：如果一台4核的物理机，cpu_allocation_ratio=2，那么最大可以创建多少核的虚拟机


